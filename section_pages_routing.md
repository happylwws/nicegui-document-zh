# *Pages* & Routing

## Page

这个装饰器将一个函数标记为页面构建器。
每个访问给定路由的用户都将看到页面的新实例。
这意味着它是用户私有的，不与其他人共享
(就像在[将元素放置在页面装饰器之外](https://nicegui.io/documentation/section_pages_routing#auto-index_page)时所做的那样)。

注意：
* 被装饰的函数的名称未被使用，可以是任何名称。
* 页面路由由 `path` 参数确定，并在全局注册。
* 装饰器仅适用于自由函数和静态方法。
实例方法或初始化器需要一个 `self` 参数，路由器无法将其关联。
有关组织代码的策略，请参阅[我们的模块化示例](https://github.com/zauberzeug/nicegui/tree/main/examples/modularization/)。

| 参数 | 描述 |
| --- | --- |
| path: | 新页面的路由（路径必须以“/”开头） |
| title: | 可选的页面标题 |
| viewport: | 可选的 viewport meta 标签内容 |
| favicon: | favicon 的可选相对文件路径或绝对 URL（默认值：`None`，将使用 NiceGUI 图标） |
| dark: | 是否使用 Quasar 的深色模式（默认为 `run` 命令的 `dark` 参数） |
| language: | 页面语言（默认为 `run` 命令的 `language` 参数） |
| response_timeout: | 构建页面的装饰函数的最大时间（默认：3.0 秒） |
| reconnect_timeout: | 服务器等待浏览器重新连接的最大时间（默认为 `run` 命令的 `reconnect_timeout` 参数） |
| api_router: | 要使用的 APIRouter 实例，可以保留 `None` 以使用默认值 |
| kwargs: | 传递给 FastAPI 的 @app.get 方法的额外关键字参数 |

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

更多信息...

## Auto-index page

使用 `@ui.page` 装饰器创建的页面是“私有的”。
它们的内容是为每个客户端重新创建的。
因此，在右侧的演示中，当浏览器重新加载页面时，私有页面上显示的 ID 会更改。

未包装在装饰的页面函数中的 UI 元素放置在路由为“/”的自动生成的索引页面上。
此自动索引页面在启动时创建一次，并在所有可能连接的客户端之间*共享*。
因此，每个连接的客户端将看到*相同*的元素。
在右侧的演示中，当浏览器重新加载页面时，自动索引页面上显示的 ID 保持不变。

```python
from nicegui import ui
from uuid import uuid4

@ui.page('/private_page')
async def private_page():
    ui.label(f'private page with ID {uuid4()}')

ui.label(f'shared auto-index page with ID {uuid4()}')
ui.link('private page', private_page)

ui.run()
```

## Page Layout

使用 `ui.header`、`ui.footer`、`ui.left_drawer` 和 `ui.right_drawer`，您可以向页面添加额外的布局元素。
`fixed` 参数控制元素是否应该滚动或保持在屏幕上固定。
`top_corner` 和 `bottom_corner` 参数指示抽屉是否应该扩展到页面的顶部或底部。
有关可能的属性的更多信息，请参阅 [https://quasar.dev/layout/header-and-footer](https://quasar.dev/layout/header-and-footer) 和 [https://quasar.dev/layout/drawer](https://quasar.dev/layout/drawer)。
使用 `ui.page_sticky`，您可以将元素“粘性”地放置在屏幕上。
有关更多信息，请参阅 [https://quasar.dev/layout/page-sticky](https://quasar.dev/layout/page-sticky)。

```python
from nicegui import ui

@ui.page('/page_layout')
def page_layout():
    ui.label('CONTENT')
    [ui.label(f'Line {i}') for i in range(100)]
    with ui.header(elevated=True).style('background-color: #3874c8').classes('items-center justify-between'):
        ui.label('HEADER')
        ui.button(on_click=lambda: right_drawer.toggle(), icon='menu').props('flat color=white')
    with ui.left_drawer(top_corner=True, bottom_corner=True).style('background-color: #d7e3f4'):
        ui.label('LEFT DRAWER')
    with ui.right_drawer(fixed=False).style('background-color: #ebf1fa').props('bordered') as right_drawer:
        ui.label('RIGHT DRAWER')
    with ui.footer().style('background-color: #3874c8'):
        ui.label('FOOTER')

ui.link('show page with fancy layout', page_layout)

ui.run()
```

更多信息...

## Parameter injection

感谢 FastAPI，页面函数接受可选参数来提供[路径参数](https://fastapi.tiangolo.com/tutorial/path-params/)、[查询参数](https://fastapi.tiangolo.com/tutorial/query-params/)或整个传入的[请求](https://fastapi.tiangolo.com/advanced/using-request-directly/)，用于访问正文有效负载、标头、cookie 等。

```python
from nicegui import ui

@ui.page('/icon/{icon}')
def icons(icon: str, amount: int = 1):
    ui.label(icon).classes('text-h3')
    with ui.row():
        [ui.icon(icon).classes('text-h3') for _ in range(amount)]
ui.link('Star', '/icon/star?amount=5')
ui.link('Home', '/icon/home')
ui.link('Water', '/icon/water_drop?amount=3')

ui.run()
```

## Page title

为当前客户端设置页面标题。

| 参数 | 描述 |
| --- | --- |
| title: | 页面标题 |

```python
from nicegui import ui

ui.button('Change page title', on_click=lambda: ui.page_title('New Title'))

ui.run()
```

更多信息...

## Navigation functions

这些函数允许您在浏览器历史记录和外部 URL 中导航。

```python
from nicegui import ui

with ui.row():
    ui.button('Back', on_click=ui.navigate.back)
    ui.button('Forward', on_click=ui.navigate.forward)
    ui.button(icon='savings',
              on_click=lambda: ui.navigate.to('https://github.com/sponsors/zauberzeug'))

ui.run()
```

更多信息...

## ui.open

`ui.open` 函数已弃用。
请改用[ `ui.navigate.to`](#ui_navigate_to_(formerly_ui_open))。

## Download

触发下载文件、URL 或字节的函数。

| 参数 | 描述 |
| --- | --- |
| src: | 目标 URL、文件的本地路径或应该下载的原始数据 |
| filename: | 要下载的文件名（默认值：服务器上的文件名） |
| media_type: | 要下载的文件的媒体类型（默认值：“”） |

```python
from nicegui import ui

ui.button('Logo', on_click=lambda: ui.download('https://nicegui.io/logo.png'))

ui.run()
```

更多信息...

## Add a directory of static files

`add_static_files()` 使本地目录在指定的端点（例如 `/static`）处可用。
这对于向前端提供本地数据（如图像）很有用。
否则，浏览器将无法访问这些文件。
请只将非安全关键文件放在此处，因为它们对所有人都是可访问的。

要使单个文件可访问，可以使用 `add_static_file()`。
对于应流式传输的媒体文件，可以使用 `add_media_files()` 或 `add_media_file()`。

| 参数 | 描述 |
| --- | --- |
| url_path: | 以斜杠“/”开头的字符串，用于标识应在其上提供文件的路径 |
| local_directory: | 包含要作为静态内容提供的文件的本地文件夹 |
| follow_symlink: | 是否遵循符号链接（默认值：False） |
| max_cache_age: | 在 Cache-Control 标头中设置的 max-age 值 |

```python
from nicegui import app, ui

app.add_static_files('/examples', 'examples')
ui.label('Some NiceGUI Examples').classes('text-h5')
ui.link('AI interface', '/examples/ai_interface/main.py')
ui.link('Custom FastAPI app', '/examples/fastapi/main.py')
ui.link('Authentication', '/examples/authentication/main.py')

ui.run()
```

## Add directory of media files

`add_media_files()` 允许从指定的端点（例如 `/media`）流式传输本地文件。
这应该用于媒体文件以支持正确的流式传输。
否则，浏览器将无法增量访问和加载文件，也无法跳转到流中的不同位置。
请只将非安全关键文件放在此处，因为它们对所有人都是可访问的。

要通过流式传输使单个文件可访问，可以使用 `add_media_file()`。
对于小型静态文件，可以使用 `add_static_files()` 或 `add_static_file()`。

| 参数 | 描述 |
| --- | --- |
| url_path: | 以斜杠“/”开头的字符串，用于标识应在其上提供文件的路径 |
| local_directory: | 包含要作为媒体内容提供的文件的本地文件夹 |

```python
import requests
from nicegui import app, ui
from pathlib import Path

media = Path('media')
media.mkdir(exist_ok=True)
r = requests.get('https://cdn.coverr.co/videos/coverr-cloudy-sky-2765/1080p.mp4')
(media / 'clouds.mp4').write_bytes(r.content)
app.add_media_files('/my_videos', media)
ui.video('/my_videos/clouds.mp4')

ui.run()
```

## Add HTML to the page

您可以通过调用 `ui.add_head_html` 或 `ui.add_body_html` 向页面添加 HTML。
这对于添加自定义 CSS 样式或 JavaScript 代码很有用。

```python
from nicegui import ui

ui.add_head_html('''
    <style>
        .my-red-label {
            color: Crimson;
            font-weight: bold;
        }
    </style>
''')
ui.label('RED').classes('my-red-label')

ui.run()
```

## API Responses

NiceGUI 基于 [FastAPI](https://fastapi.tiangolo.com/)。
这意味着您可以使用 FastAPI 的所有功能。
例如，除了图形用户界面之外，您还可以实现 RESTful API。
您只需从 `nicegui` 导入 `app` 对象即可。
或者，您可以使用 `ui.run_with(app)` 而不是使用 `ui.run()` 自动启动服务器，从而在您自己的 FastAPI 应用之上运行 NiceGUI。

您还可以在页面函数中返回任何其他 FastAPI 响应对象。
例如，您可以返回一个 `RedirectResponse`，以便在满足某些条件时将用户重定向到另一个页面。
这在我们的 [身份验证演示](https://github.com/zauberzeug/nicegui/tree/main/examples/authentication/main.py) 中使用。

```python
import random
from nicegui import app, ui

@app.get('/random/{max}')
def generate_random_number(max: int):
    return {'min': 0, 'max': max, 'value': random.randint(0, max)}

max = ui.number('max', value=100)
ui.button('generate random number',
          on_click=lambda: ui.navigate.to(f'/random/{max.value:.0f}'))

ui.run()
```
