# Configuration &amp; Deployment

## URLs

你可以通过 `app.urls` 访问 NiceGUI 应用程序可用的所有 URL 列表。
这些 URL 在 `app.on_startup` 中不可用，因为服务器尚未运行。
相反，你可以在页面函数中访问它们，或者使用 `app.urls.on_change` 注册一个回调。

```python
from nicegui import app, ui

@ui.page('/')
def index():
    for url in app.urls:
        ui.link(url, target=url)

ui.run()
```

## ui.run

你可以使用可选参数调用 `ui.run()`。
它们中的大多数仅在停止并完全重启应用程序后才适用，并且不适用于自动重新加载。

| 参数 | 描述 |
| --- | --- |
| host: | 使用此主机启动服务器（在原生模式下默认为 `'127.0.0.1'`，否则为 `'0.0.0.0'`） |
| port: | 使用此端口（正常模式下默认为 8080，原生模式下为自动确定的开放端口） |
| title: | 页面标题（默认: `'NiceGUI'`，可以被每个页面覆盖） |
| viewport: | 页面 meta 视口内容（默认: `'width=device-width, initial-scale=1'`，可以被每个页面覆盖） |
| favicon: | favicon 的相对文件路径、绝对 URL（默认: `None`，将使用 NiceGUI 图标）或 emoji（例如: `'🚀'`，适用于大多数浏览器） |
| dark: | 是否使用 Quasar 的深色模式（默认: `False`，使用 `None` 表示“自动”模式） |
| language: | Quasar 元素的语言（默认: `'en-US'`） |
| binding_refresh_interval: | 绑定更新之间的时间间隔（默认: `0.1` 秒，越大对 CPU 越友好） |
| reconnect_timeout: | 服务器等待浏览器重新连接的最长时间（默认: 3.0 秒） |
| message_history_length: | 连接中断后将存储和重新发送的最大消息数（默认: 1000，使用 0 禁用） |
| fastapi_docs: | 启用 FastAPI 的自动文档，包括 Swagger UI、ReDoc 和 OpenAPI JSON (布尔值或字典，详见 <a href="#system-message-1">`这里&lt;https://fastapi.tiangolo.com/tutorial/metadata/&gt;`_</a>, 默认: `False`) |
| show: | 自动在浏览器标签页中打开 UI（默认: `True`） |
| on_air: | 技术预览：如果设置为 `True`，<a class="reference external" href="https://nicegui.io/documentation/section_configuration_deployment#nicegui_on_air">允许临时远程访问</a>（默认: 禁用） |
| native: | 在 800x600 大小的原生窗口中打开 UI（默认: `False`，停用 `show`，自动查找开放端口） |
| window_size: | 在具有指定大小的原生窗口中打开 UI（例如: `(1024, 786)`，默认: `None`，也会激活 `native`） |
| fullscreen: | 在全屏窗口中打开 UI（默认: `False`，也会激活 `native`） |
| frameless: | 在无边框窗口中打开 UI（默认: `False`，也会激活 `native`） |
| reload: | 在文件更改时自动重新加载 UI（默认: `True`） |
| uvicorn_logging_level: | uvicorn 服务器的日志记录级别（默认: `'warning'`） |
| uvicorn_reload_dirs: | 用于监视的目录的逗号分隔列表的字符串（默认为仅当前工作目录） |
| uvicorn_reload_includes: | 触发修改时重新加载的 glob 模式的逗号分隔列表的字符串（默认: `'*.py'`） |
| uvicorn_reload_excludes: | 应该忽略重新加载的 glob 模式的逗号分隔列表的字符串（默认: `'.*, .py[cod], .sw.*, ~*'`） |
| tailwind: | 是否使用 Tailwind（实验性，默认: `True`） |
| prod_js: | 是否使用 Vue 和 Quasar 依赖项的生产版本（默认: `True`） |
| endpoint_documentation: | 控制哪些端点出现在自动生成的 OpenAPI 文档中（默认: 'none', 选项: 'none', 'internal', 'page', 'all'） |
| storage_secret: | 基于浏览器的存储的密钥（默认: `None`，需要一个值来启用 ui.storage.individual 和 ui.storage.browser） |
| show_welcome_message: | 是否显示欢迎消息（默认: `True`） |
| kwargs: | 其他关键字参数将传递给 `uvicorn.run` |

```python
from nicegui import ui

ui.label('page with custom title')

ui.run(title='My App')
```

See [more...](run)

## Native Mode

你可以通过在 `ui.run` 函数中指定 `native=True` 来为 NiceGUI 启用原生模式。
要自定义初始窗口大小和显示模式，请分别使用 `window_size` 和 `fullscreen` 参数。
此外，你可以通过 `app.native.window_args` 和 `app.native.start_args` 提供额外的关键字参数。
选择任何参数，因为它由内部使用的 <a href="https://pywebview.flowrl.com/guide/api.html">pywebview 模块</a> 为 `webview.create_window` 和 `webview.start` 函数定义。
请注意，这些关键字参数将优先于 `ui.run` 中定义的参数。

此外，你可以通过 `app.native.settings` 更改 `webview.settings`。

在原生模式下，`app.native.main_window` 对象允许你访问底层窗口。
它是 <a href="https://pywebview.flowrl.com/guide/api.html#window-object">pywebview 的 <code>Window</code></a> 的异步版本。

```python
from nicegui import app, ui

app.native.window_args['resizable'] = False
app.native.start_args['debug'] = True
app.native.settings['ALLOW_DOWNLOADS'] = True

ui.label('app running in native mode')
ui.button('enlarge', on_click=lambda: app.native.main_window.resize(1000, 700))

ui.run(native=True, window_size=(400, 300), fullscreen=False)
```

如果 webview 在查找所需库时遇到问题，你可能会收到与“WebView2Loader.dll”相关的错误。
要解决此问题，请尝试将 DLL 文件向上移动一个目录，例如：

* 从 `.venv/Lib/site-packages/webview/lib/x64/WebView2Loader.dll`
* 到 `.venv/Lib/site-packages/webview/lib/WebView2Loader.dll`

## Environment Variables

你可以设置以下环境变量来配置 NiceGUI：

*   `MATPLOTLIB` (默认: true) 可以设置为 `false` 以避免可能代价高昂的 Matplotlib 导入。
    这将使 `ui.pyplot` 和 `ui.line_plot` 不可用。
*   `NICEGUI_STORAGE_PATH` (默认: 本地 ".nicegui") 可以设置为更改存储文件的位置。
*   `MARKDOWN_CONTENT_CACHE_SIZE` (默认: 1000): 内存中缓存的最大 Markdown 内容片段数。
*   `RST_CONTENT_CACHE_SIZE` (默认: 1000): 内存中缓存的最大 ReStructuredText 内容片段数。

```python
from nicegui import ui
from nicegui.elements import markdown

ui.label(f'Markdown content cache size is {markdown.prepare_content.cache_info().maxsize}')

ui.run()
```

## Custom Vue Components

你可以通过子类化 `ui.element` 并实现相应的 Vue 组件来创建自定义组件。
<a href="https://github.com/zauberzeug/nicegui/tree/main/examples/custom_vue_component">“自定义 Vue 组件”示例</a>
演示了如何创建一个自定义计数器组件，该组件发出事件并接收来自服务器的更新。

<a href="https://github.com/zauberzeug/nicegui/blob/main/examples/signature_pad">“签名板”示例</a>
展示了如何使用 `package.json` 文件为自定义组件定义依赖项。
这允许你在组件中使用 NPM 的第三方库。

最后但并非最不重要的一点是，<a href="https://github.com/zauberzeug/nicegui/blob/main/examples/node_module_integration">“节点模块集成”示例</a>
演示了如何创建 package.json 文件和 webpack.config.js 文件，以将自定义 Vue 组件及其依赖项捆绑在一起。

## Server Hosting

要在服务器上部署你的 NiceGUI 应用程序，你需要通过云基础设施执行你的 `main.py`（或包含 `ui.run(...)` 的任何文件）。
例如，你可以通过 pip 安装 <a href="https://pypi.org/project/nicegui/">NiceGUI Python 包</a>，并使用 systemd 或类似服务来启动主脚本。
在大多数情况下，你将在 `ui.run` 命令中将端口设置为 80（或者如果你想使用 HTTPS 则设置为 443），以便可以从外部轻松访问。

一个方便的替代方法是使用我们的 <a href="https://hub.docker.com/r/zauberzeug/nicegui">预构建的多架构 Docker 镜像</a>，其中包含所有必要的依赖项。
使用此命令，你可以在公共端口 80 上启动当前目录中的脚本 `main.py`：

```bash
docker run -it --restart always \
-p 80:8080 \
-e PUID=$(id -u) \
-e PGID=$(id -g) \
-v $(pwd)/:/app/ \
zauberzeug/nicegui:latest
```

该演示假设 `main.py` 在 `ui.run` 命令中使用端口 8080（这是默认值）。
`-d` 告诉 docker 在后台运行，`--restart always` 确保如果应用程序崩溃或服务器重新启动，容器会重新启动。
当然，这也可以写在 Docker Compose 文件中：

```yaml
app:
    image: zauberzeug/nicegui:latest
    restart: always
    ports:
        - 80:8080
    environment:
        - PUID=1000 # change this to your user id
        - PGID=1000 # change this to your group id
    volumes:
        - ./:/app/
```

Docker 镜像中还有其他方便的功能，例如非 root 用户执行和信号传递。
有关更多详细信息，我们建议查看我们的 <a href="https://github.com/zauberzeug/nicegui/tree/main/examples/docker_image">Docker 示例</a>。

要使用 <a href="https://fastapi.tiangolo.com/deployment/https/">HTTPS</a> 加密来服务你的应用程序，你可以通过多种方式提供 SSL 证书。
例如，你可以通过将相关的 <a href="https://www.uvicorn.org/#command-line-options">选项</a> 传递给 `ui.run()`，直接向 <a href="https://www.uvicorn.org/">Uvicorn</a>（NiceGUI 基于它）提供你的证书：

```python
from nicegui import ui

ui.run(
    port=443,
    ssl_certfile="<path_to_certfile>",
    ssl_keyfile="<path_to_keyfile>",
)
```

在生产环境中，我们也喜欢使用 <a href="https://doc.traefik.io/traefik/">Traefik</a> 或 <a href="https://www.nginx.com/">NGINX</a> 等反向代理来为我们处理这些细节。
请参阅我们基于 Traefik 的开发 <a href="https://github.com/zauberzeug/nicegui/blob/main/docker-compose.yml">docker-compose.yml</a> 示例，或
<a href="https://github.com/zauberzeug/nicegui/blob/main/examples/nginx_https/nginx.conf">此示例 nginx.conf 文件</a>，展示了如何使用 NGINX 处理 SSL 证书和反向代理到你的 NiceGUI 应用程序。

你还可以查看 <a href="https://github.com/zauberzeug/nicegui/tree/main/examples/fastapi">我们使用自定义 FastAPI 应用程序的演示</a>。
这将允许你按照 <a href="https://fastapi.tiangolo.com/deployment/">FastAPI 文档</a>中的描述进行非常灵活的部署。
请注意，需要额外的步骤来允许多个 worker。

## Package for Installation

NiceGUI 应用程序也可以使用基于 <a href="https://www.pyinstaller.org/">PyInstaller</a> 的 `nicegui-pack` 打包成可执行文件。
这允许你将你的应用程序作为可以在任何计算机上执行的单个文件分发。

只需确保在你的主脚本中使用 `reload=False` 调用 `ui.run` 以禁用自动重新加载功能。
运行下面的 `nicegui-pack` 命令将在 `dist` 文件夹中创建一个可执行文件 `myapp`：

```python
from nicegui import native, ui

ui.label('Hello from PyInstaller')

ui.run(reload=False, port=native.find_open_port())
```

```bash
nicegui-pack --onefile --name "myapp" main.py
```

**打包提示：**

*   构建 PyInstaller 应用程序时，你的主脚本可以使用原生窗口（而不是浏览器窗口），方法是
    使用 `ui.run(reload=False, native=True)`。
    `native` 参数可以是 `True` 或 `False`，具体取决于你想要原生窗口还是在用户的浏览器中启动
    页面 - 两者都可以在 PyInstaller 生成的应用程序中工作。
*   为 `nicegui-pack` 指定 `--windowed` 将阻止终端控制台的出现。
    但是，只有当你也在 `ui.run` 命令中指定了 `native=True` 时才应使用此选项。
    没有终端控制台，用户将无法通过按 Ctrl-C 退出应用程序。
    使用 `native=True` 选项，该应用程序将在窗口关闭时自动关闭，正如预期的那样。
*   为 `nicegui-pack` 指定 `--windowed` 将在 Mac 上创建一个 `.app` 文件，这可能更方便分发。
    当你双击该应用程序以运行时，它将不会显示任何控制台输出。
    你也可以从命令行使用 `./myapp.app/Contents/MacOS/myapp` 运行该应用程序以查看控制台输出。
*   为 `nicegui-pack` 指定 `--onefile` 将创建一个单独的可执行文件。
    虽然方便分发，但启动速度会较慢。
    这不是 NiceGUI 的错误，而是 Pyinstaller 将内容压缩到单个文件中的方式，然后在运行之前将所有内容解压缩到临时目录中。
    你可以通过从 `nicegui-pack` 命令中删除 `--onefile` 来缓解此问题，
    并自行压缩生成的 `dist` 目录，分发它，
    你的最终用户可以解压缩一次，然后就可以使用了，无需由于 `--onefile` 标志而不断扩展文件。
*   不同选项的用户体验摘要：

| `nicegui-pack` | `ui.run(...)` | 说明 |
| --- | --- | --- |
| `onefile` | `native=False` | 在 `dist/` 中生成单个可执行文件，在浏览器中运行 |
| `onefile` | `native=True` | 在 `dist/` 中生成单个可执行文件，在弹出窗口中运行 |
| `onefile` 和 `windowed` | `native=True` | 在 `dist/` 中生成单个可执行文件（在 Mac 上生成合适的 `dist/myapp.app`，包括图标），在弹出窗口中运行，不显示控制台 |
| `onefile` 和 `windowed` | `native=False` | 避免使用（无法退出应用程序） |
| 不指定 |  | 创建一个 `dist/myapp` 目录，可以手动压缩并分发；使用 `dist/myapp/myapp` 运行 |

*   如果你正在使用 Python 虚拟环境，请确保你在虚拟环境中 `pip install pyinstaller`，
    以便使用正确的 PyInstaller，否则你可能会因为选择了错误的 PyInstaller 版本而获得损坏的应用程序。
    这就是为什么 `nicegui-pack` 使用 `python -m PyInstaller` 而不是仅仅使用 `pyinstaller` 来调用 PyInstaller。

```bash
python -m venv venv
source venv/bin/activate
pip install nicegui
pip install pyinstaller
```

注意：
如果你遇到错误“TypeError: a bytes-like object is required, not 'str'”，请尝试在 `main.py` 文件的顶部添加以下行：

```python
import sys
sys.stdout = open('logs.txt', 'w')
```

有关更多信息，请参阅 <a href="https://github.com/zauberzeug/nicegui/issues/681">https://github.com/zauberzeug/nicegui/issues/681</a>。

**macOS 打包**

在主应用程序文件中的其他任何内容之前添加以下代码段，以防止在新进程中无休止地生成进程：

```python
# macOS packaging support
from multiprocessing import freeze_support  # noqa
freeze_support()  # noqa

# all your other imports and code
```

`# noqa` 注释指示 Pylance 或 autopep8 不对这两行应用任何 PEP 规则，从而保证它们保持在其他任何内容之上。
这是防止进程生成的关键。

## NiceGUI On Air

通过使用 `ui.run(on_air=True)`，你可以通过互联网与他人分享你的本地应用程序 🧞。

访问 On Air URL 时，所有库（如 Vue、Quasar 等）都从我们的 CDN 加载。
因此，只有原始内容和事件需要通过你的本地应用程序传输。
即使你的应用程序只有较差的互联网连接（例如，现场的移动机器人），这也使其速度非常快。

通过设置 `on_air=True`，你将获得一个随机 URL，有效期为 1 小时。
如果你在 <a href="https://on-air.nicegui.io">https://on-air.nicegui.io</a> 注册，你可以设置一个组织和设备名称以获取固定的 URL：
`https://on-air.nicegui.io/<my-org>/<my_device_name>`。
然后，该设备通过一个唯一的私有令牌进行标识，你可以使用该令牌代替布尔标志：`ui.run(on_air='<your token>')`。
如果你 <a href="https://github.com/sponsors/zauberzeug">赞助我们</a>，
我们将启用多设备管理，并为每个设备提供内置的密码保护。

目前，On Air 作为技术预览版提供，可以免费使用。
我们将逐步提高稳定性，并通过使用情况统计、远程终端访问等扩展服务。
请在 <a href="https://github.com/zauberzeug/nicegui/discussions">GitHub</a>、
<a href="https://www.reddit.com/r/nicegui/">Reddit</a> 或 <a href="https://discord.gg/TEpFeAaF4f">Discord</a> 上告知我们你的反馈。

**数据隐私：**
我们非常重视你的隐私。
NiceGUI On Air 不会记录或存储任何中继数据的内容。
