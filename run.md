# ui.run

## ui.run

你可以使用可选参数调用 `ui.run()`。
其中大多数参数仅在停止并完全重启应用程序后才适用，并且不适用于自动重新加载。

| 参数 | 描述 |
| --- | --- |
| host: | 使用此主机启动服务器（在原生模式下默认为 `'127.0.0.1'`，否则为 `'0.0.0.0'`） |
| port: | 使用此端口（在普通模式下默认为 8080，在原生模式下为自动确定的开放端口） |
| title: | 页面标题（默认：`'NiceGUI'`，可以为每个页面覆盖） |
| viewport: | 页面 meta viewport 内容（默认：`'width=device-width, initial-scale=1'`，可以为每个页面覆盖） |
| favicon: | favicon 的相对文件路径、绝对 URL（默认：`None`，将使用 NiceGUI 图标）或 emoji（例如 `'🚀'`，适用于大多数浏览器） |
| dark: | 是否使用 Quasar 的暗黑模式（默认：`False`，使用 `None` 表示“自动”模式） |
| language: | Quasar 元素的语言（默认：`'en-US'`） |
| binding_refresh_interval: | 绑定更新之间的时间间隔（默认：`0.1` 秒，越大对 CPU 越友好） |
| reconnect_timeout: | 服务器等待浏览器重新连接的最大时间（默认：3.0 秒） |
| message_history_length: | 连接中断后将存储并重新发送的最大消息数（默认：1000，使用 0 禁用） |
| fastapi_docs: | 启用 FastAPI 的自动文档，包括 Swagger UI、ReDoc 和 OpenAPI JSON（布尔值或字典，如 [此处](https://fastapi.tiangolo.com/tutorial/metadata/) 所述，默认：`False`） |
| show: | 自动在浏览器标签中打开 UI（默认：`True`） |
| on_air: | 技术预览：如果设置为 `True`，则允许临时远程访问（默认：禁用） |
| native: | 在大小为 800x600 的原生窗口中打开 UI（默认：`False`，禁用 `show`，自动查找开放端口） |
| window_size: | 在具有指定大小的原生窗口中打开 UI（例如 `(1024, 786)`，默认：`None`，也会激活 `native`） |
| fullscreen: | 在全屏窗口中打开 UI（默认：`False`，也会激活 `native`） |
| frameless: | 在无边框窗口中打开 UI（默认：`False`，也会激活 `native`） |
| reload: | 在文件更改时自动重新加载 UI（默认：`True`） |
| uvicorn_logging_level: | uvicorn 服务器的日志级别（默认：`'warning'`） |
| uvicorn_reload_dirs: | 用逗号分隔的目录列表字符串，用于监视（默认仅当前工作目录） |
| uvicorn_reload_includes: | 用逗号分隔的 glob 模式列表字符串，这些模式在修改时触发重新加载（默认：`'*.py'`） |
| uvicorn_reload_excludes: | 用逗号分隔的 glob 模式列表字符串，这些模式应忽略重新加载（默认：`'.*, .py[cod], .sw.*, ~*'`） |
| tailwind: | 是否使用 Tailwind（实验性，默认：`True`） |
| prod_js: | 是否使用 Vue 和 Quasar 依赖项的生产版本（默认：`True`） |
| endpoint_documentation: | 控制哪些端点出现在自动生成的 OpenAPI 文档中（默认：'none'，选项：'none'、'internal'、'page'、'all'） |
| storage_secret: | 基于浏览器的存储的密钥（默认：`None`，需要一个值才能启用 ui.storage.individual 和 ui.storage.browser） |
| show_welcome_message: | 是否显示欢迎消息（默认：`True`） |
| kwargs: | 传递给 `uvicorn.run` 的其他关键字参数 |

```python
from nicegui import ui

ui.label('page with custom title')

ui.run(title='My App')
```

## Emoji favicon

你可以使用 emoji 作为 favicon。
这在 Chrome、Firefox 和 Safari 中有效。

```python
from nicegui import ui

ui.label('NiceGUI rocks!')

ui.run(favicon='🚀')
```

## Base64 favicon

你也可以使用 base64 编码的图像作为 favicon。

```python
from nicegui import ui

ui.label('NiceGUI with a red dot!')

icon = 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFCAYAAACNbyblAAAAHElEQVQI12P4//8/w38GIAXDIBKE0DHxgljNBAAO9TXL0Y4OHwAAAABJRU5ErkJggg=='

ui.run(favicon=icon)
```

## SVG favicon

并直接使用 SVG 作为 favicon。
在 Chrome、Firefox 和 Safari 中有效。

```python
from nicegui import ui

ui.label('NiceGUI makes you smile!')

smiley = '''
    <svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
        <circle cx="100" cy="100" r="78" fill="#ffde34" stroke="black" stroke-width="3" />
        <circle cx="80" cy="85" r="8" />
        <circle cx="120" cy="85" r="8" />
        <path d="m60,120 C75,150 125,150 140,120" style="fill:none; stroke:black; stroke-width:8; stroke-linecap:round" />
    </svg>
'''

ui.run(favicon=smiley)
```

## Custom welcome message

你可以在命令行上将 `show_welcome_message` 设置为 `False` 来静音默认欢迎消息。
相反，你可以使用自定义启动处理程序打印你自己的欢迎消息。

```python
from nicegui import app, ui

ui.label('App with custom welcome message')

app.on_startup(lambda: print('Visit your app on one of these URLs:', app.urls))

ui.run(show_welcome_message=False)
```
