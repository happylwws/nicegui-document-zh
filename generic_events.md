# Generic Events

## Generic Events

大多数 UI 元素都带有预定义的事件。
例如，演示中的 "A" 按钮有一个 `on_click` 参数，该参数需要一个协程或函数。
但是，你也可以使用 `on` 方法注册一个通用的事件处理程序，如 "B"。
这允许你为 JavaScript 和 Quasar 支持的任何事件注册处理程序。

例如，你可以像 "C" 那样为 `mousemove` 事件注册一个处理程序，即使 `ui.button` 没有 `on_mousemove` 参数。
有些事件，如 `mousemove`，会非常频繁地触发。
为了避免性能问题，你可以使用 `throttle` 参数，使处理程序仅在每 `throttle` 秒调用一次（"D"）。

通用事件处理程序可以是同步的，也可以是异步的，并且可以选择接受 `GenericEventArguments` 作为参数（"E"）。
你还可以指定应该将 JavaScript 或 Quasar 事件的哪些属性传递给处理程序（"F"）。
这可以减少服务器和客户端之间需要传输的数据量。

你可以在这里找到有关支持的事件的更多信息：

* [https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement#events](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement#events) 用于 HTML 元素
* [https://quasar.dev/vue-components](https://quasar.dev/vue-components) 用于基于 Quasar 的元素（请参阅各个组件页面上的 "Events" 选项卡）

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

## Specifying event attributes

**字符串列表** 指定 JavaScript 事件对象的属性：

```python
ui.button().on('click', handle_click, ['clientX', 'clientY'])
```

**空列表** 请求*不*传递任何属性：

```python
ui.button().on('click', handle_click, [])
```

**值 `None`** 表示传递*所有*属性（默认值）：

```python
ui.button().on('click', handle_click, None)
```

**如果事件被多个参数调用**，例如 QTable 的 "row-click" `(evt, row, index) => void`，你可以定义一个参数定义列表：

```python
ui.table(...).on('rowClick', handle_click, [[], ['name'], None])
```

在此示例中，"row-click" 事件将省略第一个 `evt` 参数的所有参数，
仅发送 `row` 参数的 "name" 属性，并发送完整的 `index`。

如果检索到的事件参数列表的长度为 1，则会自动解包该参数。
因此，你可以编写

```python
ui.button().on('click', lambda e: print(e.args['clientX'], flush=True))
```

而不是

```python
ui.button().on('click', lambda e: print(e.args[0]['clientX'], flush=True))
```

请注意，默认情况下，将发送所有参数的所有可 JSON 序列化的属性。
这是为了简化新事件的注册和发现其属性。
如果带宽是一个问题，则应将参数限制为服务器上实际需要的参数。

```python
from nicegui import ui

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name'},
    {'name': 'age', 'label': 'Age', 'field': 'age'},
]
rows = [
    {'name': 'Alice', 'age': 42},
    {'name': 'Bob', 'age': 23},
]
ui.table(columns=columns, rows=rows, row_key='name') \
    .on('rowClick', ui.notify, [[], ['name'], None])

ui.run()
```

## Modifiers

你还可以包括 [key modifiers](https://vuejs.org/guide/essentials/event-handling.html#key-modifiers)（在输入框 "A" 中显示）、
修饰符组合（在输入框 "B" 中显示）和 [event modifiers](https://vuejs.org/guide/essentials/event-handling.html#mouse-button-modifiers)（在输入框 "C" 中显示）。

```python
from nicegui import ui

with ui.row():
    ui.input('A').classes('w-12').on('keydown.space', lambda: ui.notify('You pressed space.'))
    ui.input('B').classes('w-12').on('keydown.y.shift', lambda: ui.notify('You pressed Shift+Y'))
    ui.input('C').classes('w-12').on('keydown.once', lambda: ui.notify('You started typing.'))

ui.run()
```

## Custom events

使用 `emitEvent(...)` 从 JavaScript 发出自定义事件是相当容易的，可以使用 `ui.on(...)` 监听这些事件。
如果你想在 JavaScript 中发生某些事情时调用 Python 代码，这会很有用。
在此示例中，我们正在监听浏览器选项卡的 `visibilitychange` 事件。

```python
from nicegui import ui

tabwatch = ui.checkbox('Watch browser tab re-entering')
ui.on('tabvisible', lambda: ui.notify('Welcome back!') if tabwatch.value else None)
ui.add_head_html('''
    <script>
    document.addEventListener('visibilitychange', () => {
        if (document.visibilityState === 'visible') {
            emitEvent('tabvisible');
        }
    });
    </script>
''')

ui.run()
```

## Pure JavaScript events

你还可以使用 `on` 方法注册纯 JavaScript 事件处理程序。
如果你想调用 JavaScript 代码而不向服务器发送任何数据，这会很有用。
在此示例中，我们使用 `navigator.clipboard` API 将字符串复制到剪贴板。

```python
from nicegui import ui

ui.button('Copy to clipboard') \
    .on('click', js_handler='''() => {
        navigator.clipboard.writeText("Hello, NiceGUI!");
    }''')

ui.run()
```
