# ui.page

## Page

这个装饰器将一个函数标记为页面构建器。
每个访问给定路由的用户都会看到页面的一个新实例。
这意味着它是用户私有的，不与他人共享
（就像在[将元素放置在页面装饰器之外时](https://nicegui.io/documentation/section_pages_routing#auto-index_page)所做的那样）。

注意：

*   被装饰函数的名称未使用，可以是任何名称。
*   页面路由由 `path` 参数确定，并在全局注册。
*   该装饰器仅适用于自由函数和静态方法。
实例方法或初始化器将需要一个 `self` 参数，路由器无法关联该参数。
有关如何组织代码的策略，请参阅[我们的模块化示例](https://github.com/zauberzeug/nicegui/tree/main/examples/modularization/)。

| 参数 | 描述 |
| --- | --- |
| path: | 新页面的路由 (路径必须以 '/' 开头) |
| title: | 可选的页面标题 |
| viewport: | 可选的 viewport meta 标签内容 |
| favicon: | 可选的相对文件路径或绝对 URL 到网站图标 (默认: `None`，将使用 NiceGUI 图标) |
| dark: | 是否使用 Quasar 的暗模式 (默认为 `run` 命令的 `dark` 参数) |
| language: | 页面的语言 (默认为 `run` 命令的 `language` 参数) |
| response_timeout: | 装饰函数构建页面的最大时间 (默认: 3.0 秒) |
| reconnect_timeout: | 服务器等待浏览器重新连接的最大时间 (默认为 `run` 命令的 `reconnect_timeout` 参数) |
| api_router: | 要使用的 APIRouter 实例，可以保留为 `None` 以使用默认实例 |
| kwargs: | 传递给 FastAPI 的 @app.get 方法的附加关键字参数 |

```python
from nicegui import ui

@ui.page('/other_page')
def other_page():
    ui.label('Welcome to the other side')

@ui.page('/dark_page', dark=True)
def dark_page():
    ui.label('Welcome to the dark side')

ui.link('Visit other page', other_page)
ui.link('Visit dark page', dark_page)

ui.run()
```

## Pages with Path Parameters

页面路由可以包含像 [FastAPI](https://fastapi.tiangolo.com/tutorial/path-params/) 一样的参数。
如果进行了类型注解，它们会自动转换为布尔值、整数、浮点数和复数值。
如果页面函数期望一个 `request` 参数，则会自动提供请求对象。
`client` 参数提供对 WebSocket 连接、布局等的访问。

```python
from nicegui import ui

@ui.page('/repeat/{word}/{count}')
def page(word: str, count: int):
    ui.label(word * count)

ui.link('Say hi to Santa!', '/repeat/Ho! /3')

ui.run()
```

## Wait for Client Connection

要等待客户端连接，你可以向装饰的页面函数添加一个 `client` 参数，并等待 `client.connected()`。
该语句下面的所有代码在服务器和客户端之间的 WebSocket 连接建立后执行。

例如，这允许你运行 JavaScript 命令；只有在客户端连接的情况下才有可能（参见[#112](https://github.com/zauberzeug/nicegui/issues/112)）。
此外，可以在用户已经看到一些内容的同时执行异步操作。

```python
import asyncio
from nicegui import ui

@ui.page('/wait_for_connection')
async def wait_for_connection():
    ui.label('This text is displayed immediately.')
    await ui.context.client.connected()
    await asyncio.sleep(2)
    ui.label('This text is displayed 2 seconds after the page has been fully loaded.')

ui.link('wait for connection', wait_for_connection)

ui.run()
```

## Multicasting

页面上的内容是客户端（浏览器选项卡）私有的，并具有其自己的本地元素上下文。
如果要向特定页面的*所有*客户端发送更新，可以使用 `app.clients` 迭代器。
这对于从后台进程或其他页面修改 UI 元素非常有用。

```python
from nicegui import app, ui

@ui.page('/multicast_receiver')
def page():
    ui.label('This page will show messages from the index page.')

def send(message: str):
    for client in app.clients('/multicast_receiver'):
        with client:
            ui.notify(message)

ui.button('Send message', on_click=lambda: send('Hi!'))
ui.link('Open receiver', '/multicast_receiver', new_tab=True)

ui.run()
```

## Modularize with APIRouter

你可以使用 NiceGUI 专门化的 [FastAPI 的 APIRouter](https://fastapi.tiangolo.com/tutorial/bigger-applications/?h=apirouter#apirouter) 来通过将页面和其他路由分组在一起来模块化你的代码。
如果你想为多个页面重用相同的前缀，这将特别有用。
路由器及其页面可以整齐地放置在单独的模块（例如文件）中，并且路由器只是被导入并包含在主应用程序中。
有关使用 API 路由器的多文件应用程序结构，请参阅[我们的模块化示例](https://github.com/zauberzeug/nicegui/blob/main/examples/modularization/api_router_example.py)。

```python
from nicegui import APIRouter, app, ui

router = APIRouter(prefix='/sub-path')

@router.page('/')
def page():
    ui.label('This is content on /sub-path')

@router.page('/sub-sub-path')
def page():
    ui.label('This is content on /sub-path/sub-sub-path')

ui.link('Visit sub-path', '/sub-path')
ui.link('Visit sub-sub-path', '/sub-path/sub-sub-path')

app.include_router(router)

ui.run()
```
