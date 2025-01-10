# Read and write to the clipboard

## Read and write to the clipboard

以下演示展示了如何使用 `ui.clipboard.read()` 和 `ui.clipboard.write()` 与剪贴板进行交互。

由于可以同时通过多个浏览器标签访问自动索引页面，因此此页面不支持读取剪贴板。
这仅在用 `ui.page` 修饰的页面构建器函数中才有可能，如此演示所示。

请注意，您的浏览器可能会要求允许访问剪贴板，或者可能根本不支持此功能。

```python
from nicegui import ui

@ui.page('/')
async def index():
    ui.button('Write', on_click=lambda: ui.clipboard.write('Hi!'))

    async def read() -> None:
        ui.notify(await ui.clipboard.read())
    ui.button('Read', on_click=read)

ui.run()
```

## Client-side clipboard

为了避免往返服务器，您还可以直接使用客户端剪贴板 API。
这可能被更多浏览器支持，因为剪贴板访问是直接由用户操作触发的。

```python
from nicegui import ui

ui.button('Write').on('click', js_handler='''
    () => navigator.clipboard.writeText("Ho!")
''')
ui.button('Read').on('click', js_handler='''
    async () => emitEvent("clipboard", await navigator.clipboard.readText())
''')
ui.on('clipboard', lambda e: ui.notify(e.args))

ui.run()
```
