# dash的研究

Author: `nanak `

Finished:  `2023/10/7 13:18`

## 初始化

在dash.py下530行有如下代码

```python
def init_app(self, app=None, **kwargs):
        """Initialize the parts of Dash that require a flask app."""

        config = self.config

        config.update(kwargs)
        config.set_read_only(
            [
                "url_base_pathname",
                "routes_pathname_prefix",
                "requests_pathname_prefix",
            ],
            "Read-only: can only be set in the Dash constructor or during init_app()",
        )
		# 在这里被赋值给self.server,即将 Flask 应用程序与 Dash 实例关联起来
        if app is not None:
            self.server = app

        bp_prefix = config.routes_pathname_prefix.replace("/", "_").replace(".", "_")
        assets_blueprint_name = f"{bp_prefix}dash_assets"

        self.server.register_blueprint(
            flask.Blueprint(
                assets_blueprint_name,
                config.name,
                static_folder=self.config.assets_folder,
                static_url_path=config.routes_pathname_prefix
                + self.config.assets_url_path.lstrip("/"),
            )
        )

        if config.compress:
            try:
                # pylint: disable=import-outside-toplevel
                from flask_compress import Compress

                # gzip
                Compress(self.server)

                _flask_compress_version = parse_version(
                    get_distribution("flask-compress").version
                )

                if not hasattr(
                    self.server.config, "COMPRESS_ALGORITHM"
                ) and _flask_compress_version >= parse_version("1.6.0"):
                    # flask-compress==1.6.0 changed default to ['br', 'gzip']
                    # and non-overridable default compression with Brotli is
                    # causing performance issues
                    self.server.config["COMPRESS_ALGORITHM"] = ["gzip"]
            except ImportError as error:
                raise ImportError(
                    "To use the compress option, you need to install dash[compress]"
                ) from error

        @self.server.errorhandler(PreventUpdate)
        def _handle_error(_):
            """Handle a halted callback and return an empty 204 response."""
            return "", 204

        self.server.before_request(self._setup_server)

        # add a handler for components suites errors to return 404
        self.server.errorhandler(InvalidResourceError)(self._invalid_resources_handler)

        self._setup_routes()

        _get_app.APP = self
        self.enable_pages()

        self._setup_plotlyjs()
```

这段代码主要是在初始化 Dash 应用程序时设置一些必要的配置和处理函数，以及与 Flask 应用程序的集成。它为后续的应用程序运行和交互提供了必要的基础。

有这样的代码在613行

```python
def _setup_routes(self):
        self._add_url(
            "_dash-component-suites/<string:package_name>/<path:fingerprinted_path>",
            self.serve_component_suites,
        )
        self._add_url("_dash-layout", self.serve_layout)
        self._add_url("_dash-dependencies", self.dependencies)
        self._add_url("_dash-update-component", self.dispatch, ["POST"])
        self._add_url("_reload-hash", self.serve_reload_hash)
        self._add_url("_favicon.ico", self._serve_default_favicon)
        self._add_url("", self.index)

        if jupyter_dash.active:
            self._add_url(
                "_alive_" + jupyter_dash.alive_token, jupyter_dash.serve_alive
            )

        # catch-all for front-end routes, used by dcc.Location
        self._add_url("<path:path>", self.index)
```

着重看623行`self._add_url("", self.index)`，这句代码的意思是注册一个默认路由`/`，也就是我们默认访问的http://127.0.0.1:8081/，的url。

以上602行有`_add_url`支持方法用于在 Dash 应用程序的 Flask 服务器上添加路由规则。代码如下。

```python
def _add_url(self, name, view_func, methods=("GET",)):
        full_name = self.config.routes_pathname_prefix + name

        self.server.add_url_rule(
            full_name, view_func=view_func, endpoint=full_name, methods=list(methods)
        )

        # record the url in Dash.routes so that it can be accessed later
        # e.g. for adding authentication with flask_login
        self.routes.append(full_name)
```

它接受路由的名称、视图函数和请求方法（默认为 "GET"）作为参数。

以下是代码的解析：

1. 首先，根据配置中的 `routes_pathname_prefix` 属性和传入的路由名称 `name` 构建完整的路由路径 `full_name`。
2. 然后，使用 Flask 应用程序的 `add_url_rule` 方法添加路由规则。`view_func` 参数是处理该路由的视图函数，`endpoint` 参数是路由的终点名称，`methods` 参数是该路由支持的请求方法列表。
3. 将完整的路由路径 `full_name` 添加到 Dash 实例的 `routes` 属性中，以便以后可以访问。这可以用于在后续的操作中对路由进行处理，例如添加身份验证。

通过调用 `_add_url` 方法，可以将自定义的路由规则添加到 Dash 应用程序的 Flask 服务器上，以便处理特定的 URL 请求。这使得可以根据需要定义和管理不同的页面和功能。

接着，继续回到623行的`self._add_url("", self.index)`。可知是`self.index`作为函数视图处理请求。

969行有代码

```python
def index(self, *args, **kwargs):  # pylint: disable=unused-argument
        scripts = self._generate_scripts_html()
        css = self._generate_css_dist_html()
        config = self._generate_config_html()
        metas = self._generate_meta()
        renderer = self._generate_renderer()

        # use self.title instead of app.config.title for backwards compatibility
        title = self.title

        if self.use_pages and self.config.include_pages_meta:
            metas = _page_meta_tags(self) + metas

        if self._favicon:
            favicon_mod_time = os.path.getmtime(
                os.path.join(self.config.assets_folder, self._favicon)
            )
            favicon_url = f"{self.get_asset_url(self._favicon)}?m={favicon_mod_time}"
        else:
            prefix = self.config.requests_pathname_prefix
            favicon_url = f"{prefix}_favicon.ico?v={__version__}"

        favicon = format_tag(
            "link",
            {"rel": "icon", "type": "image/x-icon", "href": favicon_url},
            opened=True,
        )

        tags = "\n      ".join(
            format_tag("meta", x, opened=True, sanitize=True) for x in metas
        )

        index = self.interpolate_index(
            metas=tags,
            title=title,
            css=css,
            config=config,
            scripts=scripts,
            app_entry=_app_entry,
            favicon=favicon,
            renderer=renderer,
        )

        checks = (
            _re_index_entry_id,
            _re_index_config_id,
            _re_index_scripts_id,
            _re_renderer_scripts_id,
        )
        _validate.validate_index("index", checks, index)
        return index
```

以下是代码的解析：

1. 首先，通过调用一系列私有方法生成所需的 HTML 片段。这些方法包括 `_generate_scripts_html`、`_generate_css_dist_html`、`_generate_config_html`、`_generate_meta` 和 `_generate_renderer`。分别用于生成脚本、CSS、配置信息、元标签和渲染器的 HTML。
2. 使用 `self.title` 获取应用程序的标题。
3. 如果应用程序启用了页面功能（`self.use_pages` 为 `True`）并且配置中包括页面元标签（`self.config.include_pages_meta` 为 `True`），则调用 `_page_meta_tags` 方法生成页面元标签的 HTML。
4. 如果应用程序设置了自定义的 favicon（`self._favicon` 存在），则获取 favicon 文件的修改时间，并将其作为查询参数添加到 favicon URL 中。
5. 使用 `format_tag` 方法生成 favicon 的 HTML 标签。
6. 使用列表推导式生成元标签的 HTML。
7. 调用 `self.interpolate_index` 方法，将生成的 HTML 片段插入到主页面模板中。这个方法用于将各个片段的 HTML 插入到主页面的相应位置。
8. 对生成的主页面进行一些验证，例如检查是否存在特定的 HTML 元素和标记。
9. 返回生成的主页面 HTML。

react在这里将被调用，851行代码如下

```python
def _generate_scripts_html(self):
        # Dash renderer has dependencies like React which need to be rendered
        # before every other script. However, the dash renderer bundle
        # itself needs to be rendered after all of the component's
        # scripts have rendered.
        # The rest of the scripts can just be loaded after React but before
        # dash renderer.
        # pylint: disable=protected-access

        mode = "dev" if self._dev_tools["props_check"] is True else "prod"

        deps = [
            {
                key: value[mode] if isinstance(value, dict) else value
                for key, value in js_dist_dependency.items()
            }
            for js_dist_dependency in _dash_renderer._js_dist_dependencies
        ]
        dev = self._dev_tools.serve_dev_bundles
        srcs = (
            self._collect_and_register_resources(
                self.scripts._resources._filter_resources(deps, dev_bundles=dev)
            )
            + self.config.external_scripts
            + self._collect_and_register_resources(
                self.scripts.get_all_scripts(dev_bundles=dev)
                + self.scripts._resources._filter_resources(
                    _dash_renderer._js_dist, dev_bundles=dev
                )
                + self.scripts._resources._filter_resources(
                    dcc._js_dist, dev_bundles=dev
                )
                + self.scripts._resources._filter_resources(
                    html._js_dist, dev_bundles=dev
                )
                + self.scripts._resources._filter_resources(
                    dash_table._js_dist, dev_bundles=dev
                )
            )
        )

        self._inline_scripts.extend(_callback.GLOBAL_INLINE_SCRIPTS)
        _callback.GLOBAL_INLINE_SCRIPTS.clear()

        return "\n".join(
            [
                format_tag("script", src)
                if isinstance(src, dict)
                else f'<script src="{src}"></script>'
                for src in srcs
            ]
            + [f"<script>{src}</script>" for src in self._inline_scripts]
        )
```

970行代码`scripts = self._generate_scripts_html()`调用这个支持函数来设置js脚本。

`_generate_scripts_html` 方法是 Dash 应用程序中用于生成脚本（JavaScript）的 HTML 片段的私有方法。以下是代码的解析：

1. 首先，根据开发工具的配置确定所使用的模式（"dev" 或 "prod"）。
2. 获取 Dash 渲染器的依赖项（dependencies）。这些依赖项包括 React 等需要在其他脚本之前加载的库。依赖项的信息存储在 `_dash_renderer._js_dist_dependencies` 中。
3. 根据模式和依赖项的配置，生成依赖项的字典列表。如果依赖项的值是一个字典，则根据模式选择相应的值。
4. 根据开发工具的配置，确定是否使用开发模式加载脚本。
5. 通过调用 `self.scripts._resources._filter_resources` 方法，过滤和收集所需的资源（包括依赖项和其他脚本）。
6. 将外部脚本（`self.config.external_scripts`）添加到资源列表中。
7. 将各个资源列表连接在一起，形成最终的脚本列表。
8. 返回脚本列表。

`_generate_scripts_html` 方法主要负责收集和生成 Dash 应用程序中所需的脚本的 HTML 片段。这些脚本包括 Dash 渲染器的依赖项、应用程序定义的外部脚本以及其他 Dash 组件（如 dcc、html、dash_table）的脚本。

修改代码如下图，重新访问就会得到下图结果

![image-20231006224955581](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231006224955581.png)

可以看到，返回了js脚本依赖项。别忘改回去！！！

至此，react和flask被使用了。生成了`index`页面。

## 路由

![image-20231006234815296](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231006234815296.png)

这些代码用于为应用程序添加路由规则，将不同的 URL 映射到相应的处理函数上。

代码解析如下：

1. `_add_url("_dash-layout", self.serve_layout)`：将 `"/_dash-layout"` URL 映射到 `self.serve_layout` 方法上。这个方法用于返回应用程序的布局信息。
2. `_add_url("_dash-dependencies", self.dependencies)`：将 `"/_dash-dependencies"` URL 映射到 `self.dependencies` 方法上。这个方法用于返回应用程序的依赖信息，包括 JavaScript 和 CSS 文件的路径。
3. `_add_url("_dash-update-component", self.dispatch, ["POST"])`：将 `"/_dash-update-component"` URL 映射到 `self.dispatch` 方法上，并指定请求方法为 `POST`。这个方法用于处理来自前端的更新组件请求。
4. `_add_url("_reload-hash", self.serve_reload_hash)`：将 `"/_reload-hash"` URL 映射到 `self.serve_reload_hash` 方法上。这个方法用于返回应用程序的重新加载哈希值。
5. `_add_url("_favicon.ico", self._serve_default_favicon)`：将 `"/_favicon.ico"` URL 映射到 `self._serve_default_favicon` 方法上。这个方法用于返回默认的网站图标。
6. `_add_url("", self.index)`：将根 URL (`""`) 映射到 `self.index` 方法上。这个方法用于返回应用程序的主页。

通过添加这些路由规则，Dash 应用程序可以处理不同的 URL 请求，并将其路由到对应的处理函数上。这样，当用户访问特定的 URL 时，应用程序会执行相应的逻辑并返回相应的结果。

![image-20231006235035534](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231006235035534.png)

如上图，通过监听网络即可知道dash中他们的调用顺序如何。



1309行有

```python
def _setup_server(self):
        if self._got_first_request["setup_server"]:
            return
        self._got_first_request["setup_server"] = True

        # Apply _force_eager_loading overrides from modules
        eager_loading = self.config.eager_loading
        for module_name in ComponentRegistry.registry:
            module = sys.modules[module_name]
            eager = getattr(module, "_force_eager_loading", False)
            eager_loading = eager_loading or eager

        # Update eager_loading settings
        self.scripts.config.eager_loading = eager_loading

        if self.config.include_assets_files:
            self._walk_assets_directory()

        if not self.layout and self.use_pages:
            self.layout = page_container

        _validate.validate_layout(self.layout, self._layout_value())

        self._generate_scripts_html()
        self._generate_css_dist_html()

        # Copy over global callback data structures assigned with `dash.callback`
        for k in list(_callback.GLOBAL_CALLBACK_MAP):
            if k in self.callback_map:
                raise DuplicateCallback(
                    f"The callback `{k}` provided with `dash.callback` was already "
                    "assigned with `app.callback`."
                )

            self.callback_map[k] = _callback.GLOBAL_CALLBACK_MAP.pop(k)

        self._callback_list.extend(_callback.GLOBAL_CALLBACK_LIST)
        _callback.GLOBAL_CALLBACK_LIST.clear()

        _validate.validate_long_callbacks(self.callback_map)

        cancels = {}

        for callback in self.callback_map.values():
            long = callback.get("long")
            if not long:
                continue
            if "cancel_inputs" in long:
                cancel = long.pop("cancel_inputs")
                for c in cancel:
                    cancels[c] = long.get("manager")

        if cancels:
            for cancel_input, manager in cancels.items():
                # pylint: disable=cell-var-from-loop
                @self.callback(
                    Output(cancel_input.component_id, "id"),
                    cancel_input,
                    prevent_initial_call=True,
                    manager=manager,
                )
                def cancel_call(*_):
                    job_ids = flask.request.args.getlist("cancelJob")
                    executor = _callback.context_value.get().background_callback_manager
                    if job_ids:
                        for job_id in job_ids:
                            executor.terminate_job(job_id)
                    return no_update
```

这段代码是 Dash 应用程序中的 `_setup_server` 方法的实现。它用于在应用程序启动时进行一些初始化和配置。

代码解析如下：

1. 首先，检查是否已经执行过 `_setup_server` 方法，如果是，则直接返回，避免重复执行。
2. 将 `self._got_first_request["setup_server"]` 设置为 `True`，表示已经执行过 `_setup_server` 方法。
3. 检查是否有模块中的 `_force_eager_loading` 属性被设置为 `True`，如果有，则将应用程序的 `eager_loading` 设置为 `True`。
4. 更新脚本（`scripts`）的 `eager_loading` 设置为应用程序的 `eager_loading`。
5. 如果应用程序的配置中设置了 `include_assets_files`，则调用 `_walk_assets_directory` 方法来处理静态资源文件。
6. 如果应用程序的布局为空，并且使用了页面（`use_pages` 为 `True`），则将 `page_container` 赋值给布局（`layout`）。
7. 验证应用程序的布局和布局值的兼容性，调用 `_validate.validate_layout(self.layout, self._layout_value())` 方法进行验证。
8. 调用 `_generate_scripts_html` 方法生成脚本标签的 HTML。
9. 调用 `_generate_css_dist_html` 方法生成 CSS 标签的 HTML。
10. 复制由 `dash.callback` 分配的全局回调数据结构到应用程序的回调映射中。
11. 将全局回调列表（`_callback.GLOBAL_CALLBACK_LIST`）添加到应用程序的回调列表中。
12. 验证应用程序的回调映射中的长回调。
13. 处理长回调的取消输入（cancel_inputs）。
14. 对于每个取消输入（cancel_input），创建一个回调函数来取消相关的后台任务。

这段代码的作用是在应用程序启动时进行一些初始化和配置，包括处理静态资源文件、设置布局、生成脚本和样式标签、复制全局回调数据结构等。

## 如何把注册的组件显示到前端页面

![image-20231007000945300](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007000945300.png)

在我们编写的代码会有这样一段代码。实际上`app.layout`就是一个dash实例的布局信息。

![image-20231007001352552](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007001352552.png)

如果`self.layout`不为空，且`self.use_pages`为True则使用`page_container`的布局，它用于确定是否使用自定义页面文件夹。

而我们在这里则使用，我们定义的布局

```python
app.layout = html.Div([
    dcc.Graph(
        id='basic-interactions',
        figure=fig
    ),
    html.Div(id="hover-data"),
    html.Div(id="click-data"),
])
```

这段代码自然不用我多说。

这样，调用`_setup_server`函数则会返回一个包含布局的`json`数据，而有`_add_url("_dash-layout", self.serve_layout)`：将 `"/_dash-layout"` URL 映射到 `self.serve_layout` 方法上。结果示例如下。

![image-20231007002929865](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007002929865.png)

本文初始化的部分已经讲解了如何加载前端支持脚本的以及flask如何显示页面的。这样就可以通过`React`的`props`来渲染页面了。

## 回调函数

我们有如下代码

![image-20231007122745312](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007122745312.png)

这段代码对组件绑定了两个事件回调。用法不用多说。

找到`@app.callback`的定义如下

```python
def callback(
    *_args,
    background=False,
    interval=1000,
    progress=None,
    progress_default=None,
    running=None,
    cancel=None,
    manager=None,
    cache_args_to_ignore=None,
    **_kwargs,
):
    """
    Normally used as a decorator, `@dash.callback` provides a server-side
    callback relating the values of one or more `Output` items to one or
    more `Input` items which will trigger the callback when they change,
    and optionally `State` items which provide additional information but
    do not trigger the callback directly.

    `@dash.callback` is an alternative to `@app.callback` (where `app = dash.Dash()`)
    introduced in Dash 2.0.
    It allows you to register callbacks without defining or importing the `app`
    object. The call signature is identical and it can be used instead of `app.callback`
    in all cases.

    The last, optional argument `prevent_initial_call` causes the callback
    not to fire when its outputs are first added to the page. Defaults to
    `False` and unlike `app.callback` is not configurable at the app level.

    :Keyword Arguments:
        :param background:
            Mark the callback as a long callback to execute in a manager for
            callbacks that take a long time without locking up the Dash app
            or timing out.
        :param manager:
            A long callback manager instance. Currently, an instance of one of
            `DiskcacheManager` or `CeleryManager`.
            Defaults to the `background_callback_manager` instance provided to the
            `dash.Dash constructor`.
            - A diskcache manager (`DiskcacheManager`) that runs callback
              logic in a separate process and stores the results to disk using the
              diskcache library. This is the easiest backend to use for local
              development.
            - A Celery manager (`CeleryManager`) that runs callback logic
              in a celery worker and returns results to the Dash app through a Celery
              broker like RabbitMQ or Redis.
        :param running:
            A list of 3-element tuples. The first element of each tuple should be
            an `Output` dependency object referencing a property of a component in
            the app layout. The second element is the value that the property
            should be set to while the callback is running, and the third element
            is the value the property should be set to when the callback completes.
        :param cancel:
            A list of `Input` dependency objects that reference a property of a
            component in the app's layout.  When the value of this property changes
            while a callback is running, the callback is canceled.
            Note that the value of the property is not significant, any change in
            value will result in the cancellation of the running job (if any).
        :param progress:
            An `Output` dependency grouping that references properties of
            components in the app's layout. When provided, the decorated function
            will be called with an extra argument as the first argument to the
            function.  This argument, is a function handle that the decorated
            function should call in order to provide updates to the app on its
            current progress. This function accepts a single argument, which
            correspond to the grouping of properties specified in the provided
            `Output` dependency grouping
        :param progress_default:
            A grouping of values that should be assigned to the components
            specified by the `progress` argument when the callback is not in
            progress. If `progress_default` is not provided, all the dependency
            properties specified in `progress` will be set to `None` when the
            callback is not running.
        :param cache_args_to_ignore:
            Arguments to ignore when caching is enabled. If callback is configured
            with keyword arguments (Input/State provided in a dict),
            this should be a list of argument names as strings. Otherwise,
            this should be a list of argument indices as integers.
        :param interval:
            Time to wait between the long callback update requests.
    """

    long_spec = None

    config_prevent_initial_callbacks = _kwargs.pop(
        "config_prevent_initial_callbacks", False
    )
    callback_map = _kwargs.pop("callback_map", GLOBAL_CALLBACK_MAP)
    callback_list = _kwargs.pop("callback_list", GLOBAL_CALLBACK_LIST)

    if background:
        long_spec = {
            "interval": interval,
        }

        if manager:
            long_spec["manager"] = manager

        if progress:
            long_spec["progress"] = coerce_to_list(progress)
            validate_long_inputs(long_spec["progress"])

        if progress_default:
            long_spec["progressDefault"] = coerce_to_list(progress_default)

            if not len(long_spec["progress"]) == len(long_spec["progressDefault"]):
                raise Exception(
                    "Progress and progress default needs to be of same length"
                )

        if running:
            long_spec["running"] = coerce_to_list(running)
            validate_long_inputs(x[0] for x in long_spec["running"])

        if cancel:
            cancel_inputs = coerce_to_list(cancel)
            validate_long_inputs(cancel_inputs)

            long_spec["cancel"] = [c.to_dict() for c in cancel_inputs]
            long_spec["cancel_inputs"] = cancel_inputs

        if cache_args_to_ignore:
            long_spec["cache_args_to_ignore"] = cache_args_to_ignore

    return register_callback(
        callback_list,
        callback_map,
        config_prevent_initial_callbacks,
        *_args,
        **_kwargs,
        long=long_spec,
        manager=manager,
    )
```

`callback` 用于注册回调函数。它通常作为装饰器使用，用于将一个或多个 `Output` 组件的值与一个或多个 `Input` 组件的值进行关联，并在输入组件的值发生变化时触发回调函数的执行。

`callback` 函数具有多个参数和关键字参数，这些参数用于配置回调的行为和特性。以下是一些常用的参数：

- `background`：指定回调函数是否为后台长时间执行的回调。
- `interval`：后台长时间执行回调的时间间隔。
- `progress`：指定回调函数的进度更新，可以将进度信息传递给回调函数。
- `running`：指定回调函数在运行时需要修改的组件属性。
- `cancel`：指定在回调函数运行时取消回调的触发条件。
- `manager`：指定用于执行后台长时间执行回调的管理器实例。
- `cache_args_to_ignore`：指定在启用缓存时要忽略的参数。

`callback` 函数会将上述参数和其他参数传递给 `register_callback` 函数，最终完成回调函数的注册过程。



我们现在修改一部分代码来检测一下dash关于回调函数一些重要的值。

首先，我们先在`_setup_server`这个函数下面添加一行`print`来检测一下`self.callback_map`的值。添加代码如下图。

![image-20231007123459828](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007123459828.png)

然后，在`callback`函数里添加`print`测试`_args`这个参数的值。如下图

![image-20231007123644257](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007123644257.png)

测试结果如下图

![image-20231007123715037](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007123715037.png)

可知，通过使用像如下的代码这样的装饰器即可定义回调函数。

```python
@callback(
    Output('hover-data', 'children'),
    Input('basic-interactions', 'clickData'))
def display_hover_data(hoverData):
    return str(hoverData)
```

它是由 `'basic-interactions'` 组件的 `'clickData'` 属性触发的回调。回调函数的返回值将更新到名为 `'hover-data'` 的组件的 `'children'` 属性上。

在`callback`函数的最后调用`register_callback`来注册回调。

```python
return register_callback(
        callback_list,
        callback_map,
        config_prevent_initial_callbacks,
        *_args,
        **_kwargs,
        long=long_spec,
        manager=manager,
    )
```

我们来看`register_callback`的定义

```python
def register_callback(  # pylint: disable=R0914
    callback_list, callback_map, config_prevent_initial_callbacks, *_args, **_kwargs
):
    (
        output,
        flat_inputs,
        flat_state,
        inputs_state_indices,
        prevent_initial_call,
    ) = handle_grouped_callback_args(_args, _kwargs)
    if isinstance(output, Output):
        # Insert callback with scalar (non-multi) Output
        insert_output = output
        multi = False
    else:
        # Insert callback as multi Output
        insert_output = flatten_grouping(output)
        multi = True

    long = _kwargs.get("long")
    manager = _kwargs.get("manager")

    output_indices = make_grouping_by_index(output, list(range(grouping_len(output))))
    callback_id = insert_callback(
        callback_list,
        callback_map,
        config_prevent_initial_callbacks,
        insert_output,
        output_indices,
        flat_inputs,
        flat_state,
        inputs_state_indices,
        prevent_initial_call,
        long=long,
        manager=manager,
    )
```

以下是对这段代码的解释：

- `callback_list` 和 `callback_map` 是回调函数列表和回调函数映射，用于存储已注册的回调函数。
- `config_prevent_initial_callbacks` 是一个配置参数，用于确定是否阻止初始回调。
- `_args` 和 `_kwargs` 是接受的参数，其中 `_args` 是一个元组， `_kwargs` 是一个字典。
- `handle_grouped_callback_args()` 函数用于处理传入的参数，提取输出、输入、状态等信息。
- 如果输出是 `Output` 类型的对象，表示回调函数具有单个输出；否则，回调函数具有多个输出，需要进行展平处理。
- `long` 和 `manager` 是可选的参数，应该是用于指定回调函数的其他属性。
- `make_grouping_by_index()` 函数根据索引将输出进行分组。
- `insert_callback()` 函数将回调函数插入到回调函数列表和回调函数映射中，并返回回调函数的唯一标识符 `callback_id`。

```python
long = _kwargs.get("long")
manager = _kwargs.get("manager")
```

其中这两句提取来自输出（Output）中的（前端发送给后端的json数据）`manager`和`long`

![image-20231007125212176](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007125212176.png)

`insert_callback`的定义如下

```python
def insert_callback(
    callback_list,
    callback_map,
    config_prevent_initial_callbacks,
    output,
    outputs_indices,
    inputs,
    state,
    inputs_state_indices,
    prevent_initial_call,
    long=None,
    manager=None,
):
    if prevent_initial_call is None:
        prevent_initial_call = config_prevent_initial_callbacks

    _validate.validate_duplicate_output(
        output, prevent_initial_call, config_prevent_initial_callbacks
    )

    callback_id = create_callback_id(output, inputs)
    callback_spec = {
        "output": callback_id,
        "inputs": [c.to_dict() for c in inputs],
        "state": [c.to_dict() for c in state],
        "clientside_function": None,
        # prevent_initial_call can be a string "initial_duplicates"
        # which should not prevent the initial call.
        "prevent_initial_call": prevent_initial_call is True,
        "long": long
        and {
            "interval": long["interval"],
        },
    }

    callback_map[callback_id] = {
        "inputs": callback_spec["inputs"],
        "state": callback_spec["state"],
        "outputs_indices": outputs_indices,
        "inputs_state_indices": inputs_state_indices,
        "long": long,
        "output": output,
        "raw_inputs": inputs,
        "manager": manager,
    }
    callback_list.append(callback_spec)

    return callback_id
```

- `callback_list` 是回调函数列表，用于存储已注册的回调函数的规范化表示。
- `callback_map` 是回调函数映射，用于存储已注册的回调函数的详细信息。
- `config_prevent_initial_callbacks` 是一个配置参数，用于确定是否阻止初始回调。
- `output` 是回调函数的输出。
- `outputs_indices` 是回调函数输出的索引。
- `inputs` 是回调函数的输入。
- `state` 是回调函数的状态。
- `inputs_state_indices` 是回调函数输入和状态的索引。
- `prevent_initial_call` 是一个布尔值，用于确定是否阻止初始回调。如果为 `None`，则使用 `config_prevent_initial_callbacks` 的值。
- `long` 和 `manager` 是可选的参数，用于指定回调函数的其他属性。

在函数体内部，首先根据需要确定 `prevent_initial_call` 的值。然后，使用 `_validate.validate_duplicate_output()` 函数验证回调函数的输出是否重复。

接下来，根据回调函数的输出和输入创建唯一的回调函数标识符 `callback_id`。然后，构建回调函数的规范化表示 `callback_spec`，包括输出、输入、状态等信息。

接着，将回调函数的详细信息添加到回调函数映射 `callback_map` 中，包括输入、状态、输出索引等信息。同时，将回调函数的规范化表示 `callback_spec` 添加到回调函数列表 `callback_list` 中。

最后，返回回调函数的唯一标识符 `callback_id`。

`callback_list` 和`callback_map` 的定义在这里

![image-20231007130027287](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007130027287.png)

这段代码是从 `kwargs` 字典中提取 `"callback_map"` 和 `"callback_list"` 参数的值，并将其赋给 `callback_map` 和 `callback_list` 变量。如果 `kwargs` 字典中没有这些参数，则使用默认值 `GLOBAL_CALLBACK_MAP` 和 `GLOBAL_CALLBACK_LIST`。

具体解释如下：

- `_kwargs.pop("callback_map", GLOBAL_CALLBACK_MAP)` 从 `_kwargs` 字典中提取 `"callback_map"` 参数的值。如果字典中不存在 `"callback_map"` 参数，则使用默认值 `GLOBAL_CALLBACK_MAP`。这样做的目的是获取回调函数映射对象，如果用户没有提供显式的映射对象，则使用全局的默认映射对象。
- `_kwargs.pop("callback_list", GLOBAL_CALLBACK_LIST)` 从 `_kwargs` 字典中提取 `"callback_list"` 参数的值。如果字典中不存在 `"callback_list"` 参数，则使用默认值 `GLOBAL_CALLBACK_LIST`。这样做的目的是获取回调函数列表，如果用户没有提供显式的列表，则使用全局的默认列表。

这段代码的作用是在注册回调函数时，从传入的参数中获取回调函数映射和列表对象。如果用户没有提供显式的映射和列表，则使用全局的默认对象。

## dispatch

至此已经完成了回调的注册，这个时候前端就会请求`_dash-update-component`路由, 而对应的处理函数正是`dispatch`

![image-20231007130734129](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007130734129.png)

下面是`dispatch`的定义

```python
def dispatch(self):
        body = flask.request.get_json()

        g = AttributeDict({})

        g.inputs_list = inputs = body.get(  # pylint: disable=assigning-non-slot
            "inputs", []
        )
        g.states_list = state = body.get(  # pylint: disable=assigning-non-slot
            "state", []
        )
        output = body["output"]
        outputs_list = body.get("outputs") or split_callback_id(output)
        g.outputs_list = outputs_list  # pylint: disable=assigning-non-slot

        g.input_values = (  # pylint: disable=assigning-non-slot
            input_values
        ) = inputs_to_dict(inputs)
        g.state_values = inputs_to_dict(state)  # pylint: disable=assigning-non-slot
        changed_props = body.get("changedPropIds", [])
        g.triggered_inputs = [  # pylint: disable=assigning-non-slot
            {"prop_id": x, "value": input_values.get(x)} for x in changed_props
        ]

        response = (
            g.dash_response  # pylint: disable=assigning-non-slot
        ) = flask.Response(mimetype="application/json")

        args = inputs_to_vals(inputs + state)

        try:
            cb = self.callback_map[output]
            func = cb["callback"]
            g.background_callback_manager = (
                cb.get("manager") or self._background_manager
            )
            g.ignore_register_page = cb.get("long", False)

            # Add args_grouping
            inputs_state_indices = cb["inputs_state_indices"]
            inputs_state = inputs + state
            inputs_state = convert_to_AttributeDict(inputs_state)

            # update args_grouping attributes
            for s in inputs_state:
                # check for pattern matching: list of inputs or state
                if isinstance(s, list):
                    for pattern_match_g in s:
                        update_args_group(pattern_match_g, changed_props)
                update_args_group(s, changed_props)

            args_grouping = map_grouping(
                lambda ind: inputs_state[ind], inputs_state_indices
            )

            g.args_grouping = args_grouping  # pylint: disable=assigning-non-slot
            g.using_args_grouping = (  # pylint: disable=assigning-non-slot
                not isinstance(inputs_state_indices, int)
                and (
                    inputs_state_indices
                    != list(range(grouping_len(inputs_state_indices)))
                )
            )

            # Add outputs_grouping
            outputs_indices = cb["outputs_indices"]
            if not isinstance(outputs_list, list):
                flat_outputs = [outputs_list]
            else:
                flat_outputs = outputs_list

            outputs_grouping = map_grouping(
                lambda ind: flat_outputs[ind], outputs_indices
            )
            g.outputs_grouping = outputs_grouping  # pylint: disable=assigning-non-slot
            g.using_outputs_grouping = (  # pylint: disable=assigning-non-slot
                not isinstance(outputs_indices, int)
                and outputs_indices != list(range(grouping_len(outputs_indices)))
            )

        except KeyError as missing_callback_function:
            msg = f"Callback function not found for output '{output}', perhaps you forgot to prepend the '@'?"
            raise KeyError(msg) from missing_callback_function
        ctx = copy_context()
        # noinspection PyArgumentList
        response.set_data(
            ctx.run(
                functools.partial(
                    func,
                    *args,
                    outputs_list=outputs_list,
                    long_callback_manager=self._background_manager,
                    callback_context=g,
                )
            )
        )
        print(response)
        return response

    def _setup_server(self):
        if self._got_first_request["setup_server"]:
            return
        self._got_first_request["setup_server"] = True

        # Apply _force_eager_loading overrides from modules
        eager_loading = self.config.eager_loading
        for module_name in ComponentRegistry.registry:
            module = sys.modules[module_name]
            eager = getattr(module, "_force_eager_loading", False)
            eager_loading = eager_loading or eager

        # Update eager_loading settings
        self.scripts.config.eager_loading = eager_loading

        if self.config.include_assets_files:
            self._walk_assets_directory()

        if not self.layout and self.use_pages:
            self.layout = page_container

        _validate.validate_layout(self.layout, self._layout_value())

        self._generate_scripts_html()
        self._generate_css_dist_html()

        # Copy over global callback data structures assigned with `dash.callback`
        for k in list(_callback.GLOBAL_CALLBACK_MAP):
            if k in self.callback_map:
                raise DuplicateCallback(
                    f"The callback `{k}` provided with `dash.callback` was already "
                    "assigned with `app.callback`."
                )

            self.callback_map[k] = _callback.GLOBAL_CALLBACK_MAP.pop(k)

        self._callback_list.extend(_callback.GLOBAL_CALLBACK_LIST)
        _callback.GLOBAL_CALLBACK_LIST.clear()

        _validate.validate_long_callbacks(self.callback_map)

        cancels = {}

        for callback in self.callback_map.values():
            long = callback.get("long")
            if not long:
                continue
            if "cancel_inputs" in long:
                cancel = long.pop("cancel_inputs")
                for c in cancel:
                    cancels[c] = long.get("manager")

        if cancels:
            for cancel_input, manager in cancels.items():
                # pylint: disable=cell-var-from-loop
                @self.callback(
                    Output(cancel_input.component_id, "id"),
                    cancel_input,
                    prevent_initial_call=True,
                    manager=manager,
                )
                def cancel_call(*_):
                    job_ids = flask.request.args.getlist("cancelJob")
                    executor = _callback.context_value.get().background_callback_manager
                    if job_ids:
                        for job_id in job_ids:
                            executor.terminate_job(job_id)
                    return no_update
```

以下是对这段代码的解释：

- 首先，从请求的 JSON 数据中获取必要的信息，如输入、状态、输出等。这个请求是前端主动请求后端的。
- 创建一个 `AttributeDict` 类的实例 `g`，用于存储各种上下文信息。
- 将输入、状态、输出等信息存储在 `g` 中，以便后续使用。
- 通过回调函数的输出标识符 `output`，从回调函数映射 `callback_map` 中获取相应的回调函数信息。
- 设置一些上下文信息，如回调函数的管理器、是否忽略注册页面等。
- 更新 `args_grouping` 和 `outputs_grouping`，用于处理参数分组和输出分组的逻辑。
- 捕获可能的 `KeyError` 异常，如果找不到对应的回调函数。
- 创建一个上下文副本 `ctx`，并使用 `ctx.run()` 方法执行回调函数，并将结果作为响应的数据。
- 返回响应对象。

返回的响应对象实际上就是flask返回的响应对象，测试一下值。

![image-20231007131242392](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007131242392.png)

![image-20231007131256477](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007131256477.png)

可以看到有一个叫`hover-data`和一个叫`click-data`。这样前端通过返回的`json`数据就知道该给哪个DOM添加监听事件（事件回调）了

![image-20231007131559373](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007131559373.png)

然后我们点击(a,1)测试，结果如下图

![image-20231007131413856](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007131413856.png)

前端效果如下

![image-20231007131459683](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231007131459683.png)
