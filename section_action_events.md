# Action & *Events*

## Timer

创建 NiceGUI 的一个主要驱动力是需要一种简单的方法来定期更新界面，例如显示带有传入测量的图表。
定时器将以给定的间隔重复执行回调。

| 选项 | 描述 |
| --- | --- |
| interval: | 定时器被调用的间隔（可以在运行时更改） |
| callback: | 间隔到期时要执行的函数或协程 |
| active: | 是否应执行回调（可以在运行时更改） |
| once: | 回调是否仅在由 `interval` 指定的延迟后执行一次（默认值：`False`） |
| immediate: | 是否应立即执行回调（默认值：`True`，如果 `once` 为 `True`，则忽略） |

```python
from datetime import datetime
from nicegui import ui

label = ui.label()
ui.timer(1.0, lambda: label.set_text(f'{datetime.now():%X}'))

ui.run()
```

See [more...](timer)

## Keyboard

添加全局键盘事件跟踪。

`on_key` 回调接收一个 `KeyEventArguments` 对象，其中包含以下属性：

- `sender`: `Keyboard` 元素
- `client`: 客户端对象
- `action`: 一个 `KeyboardAction` 对象，包含以下属性：
  - `keydown`: 是否按下了键
  - `keyup`: 是否释放了键
  - `repeat`: 键事件是否为重复
- `key`: 一个 `KeyboardKey` 对象，包含以下属性：
  - `name`: 键的名称（例如 "a", "Enter", "ArrowLeft"；有关可能值的列表，请参见[此处](https://developer.mozilla.org/en-US/docs/Web/API/UI_Events/Keyboard_event_key_values)）
  - `code`: 键的代码（例如 "KeyA", "Enter", "ArrowLeft"）
  - `location`: 键的位置（标准键为 0，左侧键为 1，右侧键为 2，数字键盘键为 3）
- `modifiers`: 一个 `KeyboardModifiers` 对象，包含以下属性：
  - `alt`: 是否按下了 alt 键
  - `ctrl`: 是否按下了 ctrl 键
  - `meta`: 是否按下了 meta 键
  - `shift`: 是否按下了 shift 键

为方便起见，`KeyboardKey` 对象还具有以下属性：

- `is_cursorkey`: 该键是否为光标（箭头）键
- `number`: 数字键的整数值（0-9，其他键为 `None`）
- `backspace`, `tab`, `enter`, `shift`, `control`, `alt`, `pause`, `caps_lock`, `escape`, `space`,
  `page_up`, `page_down`, `end`, `home`, `arrow_left`, `arrow_up`, `arrow_right`, `arrow_down`,
  `print_screen`, `insert`, `delete`, `meta`,
  `f1`, `f2`, `f3`, `f4`, `f5`, `f6`, `f7`, `f8`, `f9`, `f10`, `f11`, `f12`: 该键是否为相应的键

| 选项 | 描述 |
| --- | --- |
| on_key: | 键盘事件发生时要执行的回调。 |
| active: | 一个布尔标志，指示是否应执行回调（默认值：`True`） |
| repeating: | 一个布尔标志，指示是否应重复发送按住的键（默认值：`True`） |
| ignore: | 当焦点位于这些元素类型之一时忽略键（默认值：`['input', 'select', 'button', 'textarea']`） |

```python
from nicegui import ui
from nicegui.events import KeyEventArguments

def handle_key(e: KeyEventArguments):
    if e.key == 'f' and not e.action.repeat:
        if e.action.keyup:
            ui.notify('f was just released')
        elif e.action.keydown:
            ui.notify('f was just pressed')
    if e.modifiers.shift and e.action.keydown:
        if e.key.arrow_left:
            ui.notify('going left')
        elif e.key.arrow_right:
            ui.notify('going right')
        elif e.key.arrow_up:
            ui.notify('going up')
        elif e.key.arrow_down:
            ui.notify('going down')

keyboard = ui.keyboard(on_key=handle_key)
ui.label('Key events can be caught globally by using the keyboard element.')
ui.checkbox('Track key events').bind_value_to(keyboard, 'active')

ui.run()
```

See [more...](keyboard)

## UI Updates

NiceGUI 尝试自动将 UI 元素的状态与客户端同步，例如，当标签文本、输入值或元素的样式/类/属性发生更改时。
在其他情况下，您可以显式调用 `element.update()` 或 `ui.update(*elements)` 进行更新。
演示代码显示了 `ui.echart` 的两种方法，其中很难自动检测 `options` 字典中的更改。

```python
from nicegui import ui
from random import random

chart = ui.echart({
    'xAxis': {'type': 'value'},
    'yAxis': {'type': 'value'},
    'series': [{'type': 'line', 'data': [[0, 0], [1, 1]]}],
})

def add():
    chart.options['series'][0]['data'].append([random(), random()])
    chart.update()

def clear():
    chart.options['series'][0]['data'].clear()
    ui.update(chart)

with ui.row():
    ui.button('Add', on_click=add)
    ui.button('Clear', on_click=clear)

ui.run()
```

## Refreshable UI functions

`@ui.refreshable` 装饰器允许您创建具有 `refresh` 方法的函数。
此方法将自动删除该函数创建的所有元素并重新创建它们。

```python
import random
from nicegui import ui

numbers = []

@ui.refreshable
def number_ui() -> None:
    ui.label(', '.join(str(n) for n in sorted(numbers)))

def add_number() -> None:
    numbers.append(random.randint(0, 100))
    number_ui.refresh()

number_ui()
ui.button('Add random number', on_click=add_number)

ui.run()
```

See [more...](refreshable)

## Async event handlers

大多数元素也支持异步事件处理程序。

注意：您还可以将 `functools.partial` 传递到 `on_click` 属性中，以使用参数包装异步函数。

```python
import asyncio
from nicegui import ui

async def async_task():
    ui.notify('Asynchronous task started')
    await asyncio.sleep(5)
    ui.notify('Asynchronous task finished')

ui.button('start async task', on_click=async_task)

ui.run()
```

## Generic Events

大多数 UI 元素都带有预定义的事件。
例如，演示中的 `ui.button`（如“A”）具有一个 `on_click` 参数，该参数期望一个协程或函数。
但是您也可以使用 `on` 方法注册一个通用事件处理程序，例如“B”。
这允许您为 JavaScript 和 Quasar 支持的任何事件注册处理程序。

例如，您可以像对 "C" 一样为 `mousemove` 事件注册一个处理程序，即使 `ui.button` 没有 `on_mousemove` 参数。
某些事件（如 `mousemove`）会非常频繁地触发。
为了避免性能问题，您可以使用 `throttle` 参数来仅每 `throttle` 秒调用一次处理程序（“D”）。

通用事件处理程序可以是同步的或异步的，并且可以选择将 `GenericEventArguments` 作为参数（“E”）。
您还可以指定应将 JavaScript 或 Quasar 事件的哪些属性传递给处理程序（“F”）。
这可以减少服务器和客户端之间需要传输的数据量。

您可以在这里找到有关支持的事件的更多信息：

- [https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement#events](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement#events) 用于 HTML 元素
- [https://quasar.dev/vue-components](https://quasar.dev/vue-components) 用于基于 Quasar 的元素（请参见各个组件页面上的“事件”选项卡）

```python
from nicegui import ui

with ui.row():
    ui.button('A', on_click=lambda: ui.notify('You clicked the button A.'))
    ui.button('B').on('click', lambda: ui.notify('You clicked the button B.'))
with ui.row():
    ui.button('C').on('mousemove', lambda: ui.notify('You moved on button C.'))
    ui.button('D').on('mousemove', lambda: ui.notify('You moved on button D.'), throttle=0.5)
with ui.row():
    ui.button('E').on('mousedown', lambda e: ui.notify(e))
    ui.button('F').on('mousedown', lambda e: ui.notify(e), ['ctrlKey', 'shiftKey'])

ui.run()
```

See [more...](generic_events)

## Running CPU-bound tasks

NiceGUI 提供了一个 `cpu_bound` 函数，用于在单独的进程中运行 CPU 密集型任务。
这对于长时间运行的计算很有用，否则会阻塞事件循环并使 UI 无响应。
该函数返回一个可以等待的 future。

注意：
该函数需要将传递的函数的整个状态传输到进程，这是通过 pickle 完成的。
建议创建自由函数或静态方法，这些方法将所有数据作为简单参数获取（即，没有类或 UI 逻辑），并返回结果，而不是将其写入类属性或全局变量中。

```python
import time
from nicegui import run, ui

def compute_sum(a: float, b: float) -> float:
    time.sleep(1)  # simulate a long-running computation
    return a + b

async def handle_click():
    result = await run.cpu_bound(compute_sum, 1, 2)
    ui.notify(f'Sum is {result}')

ui.button('Compute', on_click=handle_click)

ui.run()
```

## Running I/O-bound tasks

NiceGUI 提供了一个 `io_bound` 函数，用于在单独的线程中运行 I/O 密集型任务。
这对于长时间运行的 I/O 操作很有用，否则会阻塞事件循环并使 UI 无响应。
该函数返回一个可以等待的 future。

```python
import requests
from nicegui import run, ui

async def handle_click():
    URL = 'https://httpbin.org/delay/1'
    response = await run.io_bound(requests.get, URL, timeout=3)
    ui.notify(f'Downloaded {len(response.content)} bytes')

ui.button('Download', on_click=handle_click)

ui.run()
```

## Run JavaScript

此函数在浏览器中执行的页面上运行任意 JavaScript 代码。
必须先连接客户端，然后才能调用此函数。
要通过 ID 访问客户端 Vue 组件或 HTML 元素，请使用 JavaScript 函数 `getElement()` 或 `getHtmlElement()`。

如果等待该函数，则返回 JavaScript 代码的结果。
否则，将执行 JavaScript 代码，而不等待响应。

请注意，仅对于页面函数支持从客户端请求数据，而不支持共享的自动索引页面。

| 选项 | 描述 |
| --- | --- |
| code: | 要运行的 JavaScript 代码 |
| timeout: | 超时时间，以秒为单位（默认值：`1.0`） |
| return: | 可等待的 AwaitableResponse，可以等待以获取 JavaScript 代码的结果 |

```python
from nicegui import ui

@ui.page('/')
def page():
    def alert():
        ui.run_javascript('alert("Hello!")')

    async def get_date():
        time = await ui.run_javascript('Date()')
        ui.notify(f'Browser time: {time}')

    def access_elements():
        ui.run_javascript(f'getHtmlElement({label.id}).innerText += " Hello!"')

    ui.button('fire and forget', on_click=alert)
    ui.button('receive result', on_click=get_date)
    ui.button('access elements', on_click=access_elements)
    label = ui.label()

ui.run()
```

See [more...](run_javascript)

## Read and write to the clipboard

以下演示展示了如何使用 `ui.clipboard.read()` 和 `ui.clipboard.write()` 与剪贴板进行交互。

由于多个浏览器选项卡可以同时访问自动索引页面，因此该页面不支持读取剪贴板。
这仅在用 `ui.page` 装饰的页面构建器函数中才有可能，如本演示所示。

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

See [more...](clipboard)

## Events

您可以注册协程或函数以针对以下事件调用：

- `app.on_startup`: 在 NiceGUI 启动或重新启动时调用
- `app.on_shutdown`: 在 NiceGUI 关闭或重新启动时调用
- `app.on_connect`: 为每个连接的客户端调用（可选参数：nicegui.Client）
- `app.on_disconnect`: 为每个断开连接的客户端调用（可选参数：nicegui.Client）
- `app.on_exception`: 发生异常时调用（可选参数：exception）

当 NiceGUI 关闭或重新启动时，所有仍在执行的任务将自动取消。

```python
from datetime import datetime
from nicegui import app, ui

dt = datetime.now()

def handle_connection():
    global dt
    dt = datetime.now()
app.on_connect(handle_connection)

label = ui.label()
ui.timer(1, lambda: label.set_text(f'Last new connection: {dt:%H:%M:%S}'))

ui.run()
```

## Shut down NiceGUI

这将以编程方式停止服务器。

```python
from nicegui import app, ui

ui.button('shutdown', on_click=app.shutdown)

ui.run(reload=False)
```

## Storage

NiceGUI 为您的应用程序中的数据持久性提供了一种直接的机制。
它具有五种内置的存储类型：

- `app.storage.tab`:
  此字典存储在服务器端的内存中，对于每个未重复的选项卡会话都是唯一的，并且可以保存任意对象。
  重新启动服务器时，数据将丢失，直到 [https://github.com/zauberzeug/nicegui/discussions/2841](https://github.com/zauberzeug/nicegui/discussions/2841) 被实现。
  此存储仅在[页面构建器函数](/documentation/page) 中可用，并且需要通过 [await client.connected()](/documentation/page#wait_for_client_connection) 获取的已建立连接。
- `app.storage.client`:
  此字典也存储在服务器端的内存中，对于每个客户端连接都是唯一的，并且可以保存任意对象。
  当页面重新加载或用户导航到另一个页面时，数据将被丢弃。
  与存储在 `app.storage.tab` 中的数据（即使在服务器上可以持久存在数天）不同，`app.storage.client` 有助于缓存资源密集型对象，例如您需要保持活动的流式传输或数据库连接，以进行动态站点更新，但希望在用户离开页面或关闭浏览器后立即将其丢弃。
  此存储仅在[页面构建器函数](/documentation/page) 中可用。
- `app.storage.user`:
  存储在服务器端，每个字典都与浏览器会话 Cookie 中保存的唯一标识符关联。
  此存储对于每个用户都是唯一的，可以在其所有浏览器选项卡中访问。
  `app.storage.browser['id']` 用于标识用户。
  此存储仅在[页面构建器函数](/documentation/page) 中可用，并且需要在 `ui.run()` 中使用 `storage_secret` 参数来签署浏览器会话 Cookie。
- `app.storage.general`:
  此字典也存储在服务器端，它提供了一个所有用户都可以访问的共享存储空间。
- `app.storage.browser`:
  与前面的类型不同，此字典直接存储为浏览器会话 Cookie，在同一用户的浏览器所有选项卡之间共享。
  但是，由于 `app.storage.user` 在减少数据负载、增强安全性和提供更大存储容量方面的优势，因此通常首选 `app.storage.user`。
  默认情况下，NiceGUI 将浏览器会话的唯一标识符保存在 `app.storage.browser['id']` 中。
  此存储仅在[页面构建器函数](/documentation/page) 中可用，并且需要在 `ui.run()` 中使用 `storage_secret` 参数来签署浏览器会话 Cookie。

下表将帮助您选择存储。

| 存储类型    | `tab` | `client` | `user` | `general` | `browser` |
| ----------- | ----- | -------- | ------ | --------- | --------- |
| 位置        | 服务器   | 服务器      | 服务器    | 服务器       | 浏览器       |
| 跨选项卡    | 否    | 否       | 是    | 是         | 是         |
| 跨浏览器    | 否    | 否       | 否    | 是         | 否         |
| 跨服务器重启 | 否    | 否       | 否    | 是         | 否         |
| 跨页面重载  | 是    | 否       | 是    | 是         | 是         |
| 需要页面构建器函数 | 是   | 是       | 是    | 否         | 是         |
| 需要客户端连接 | 是   | 否       | 否    | 否         | 否         |
| 仅在响应之前写入 | 否    | 否       | 否    | 否         | 是        |
| 需要可序列化数据 | 否  | 否      | 是    | 是        | 是        |
| 需要 `storage_secret` | 否 | 否      | 是    | 否         | 是         |

```python
from nicegui import app, ui

@ui.page('/')
def index():
    app.storage.user['count'] = app.storage.user.get('count', 0) + 1
    with ui.row():
       ui.label('your own page visits:')
       ui.label().bind_text_from(app.storage.user, 'count')

ui.run(storage_secret='private key to secure the browser session cookie')
```

See [more...](storage)
