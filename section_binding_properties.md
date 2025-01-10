# *Binding* Properties

## Bindings

NiceGUI 能够将 UI 元素直接绑定到模型。
绑定可以用于 UI 元素的属性，如 text、value 或 visibility，以及作为（嵌套）类属性的模型属性。
每个元素都提供了诸如 `bind_value` 和 `bind_visibility` 之类的方法，以创建与相应属性的双向绑定。
要定义单向绑定，请使用这些方法的 `_from` 和 `_to` 变体。
只需将模型的属性作为参数传递给这些方法即可创建绑定。
值将立即更新，并在其中任何一个发生更改时更新。

```python
from nicegui import ui

class Demo:
    def __init__(self):
        self.number = 1

demo = Demo()
v = ui.checkbox('visible', value=True)
with ui.column().bind_visibility_from(v, 'value'):
    ui.slider(min=1, max=3).bind_value(demo, 'number')
    ui.toggle({1: 'A', 2: 'B', 3: 'C'}).bind_value(demo, 'number')
    ui.number().bind_value(demo, 'number')

ui.run()
```

## Bind to dictionary

这里我们将标签的文本绑定到字典。

```python
from nicegui import ui

data = {'name': 'Bob', 'age': 17}

ui.label().bind_text_from(data, 'name', backward=lambda n: f'Name: {n}')
ui.label().bind_text_from(data, 'age', backward=lambda a: f'Age: {a}')

ui.button('Turn 18', on_click=lambda: data.update(age=18))

ui.run()
```

## Bind to variable

这里我们将日期选择器的值绑定到一个裸变量。
因此，我们使用包含所有全局变量的字典 `globals()`。
此演示基于 [官方日期选择器示例]( /documentation/date#input_element_with_date_picker)。

```python
from nicegui import ui

date = '2023-01-01'

with ui.input('Date').bind_value(globals(), 'date') as date_input:
    with ui.menu() as menu:
        ui.date(on_change=lambda: ui.notify(f'Date: {date}')).bind_value(date_input)
    with date_input.add_slot('append'):
        ui.icon('edit_calendar').on('click', menu.open).classes('cursor-pointer')

ui.run()
```

## Bind to storage

绑定也适用于 [`app.storage`](/documentation/storage)。
这里我们在访问之间存储文本区域的值。
该笔记还在同一用户的所有选项卡之间共享。

```python
from nicegui import app, ui

@ui.page('/')
def index():
    ui.textarea('This note is kept between visits')\
        .classes('w-full').bind_value(app.storage.user, 'note')

ui.run()
```

## Bindable properties for maximum performance

有两种类型的绑定：

1. “可绑定属性”会自动检测写入访问并触发值传播。
   大多数 NiceGUI 元素都使用这些可绑定属性，例如 `ui.input` 中的 `value` 或 `ui.label` 中的 `text`。
   基本上，所有带有 `bind()` 方法的属性都支持这种类型的绑定。
2. 所有其他绑定有时被称为“活动链接”。
   如果您将标签文本绑定到某个字典条目或自定义数据模型的属性，
   NiceGUI 的绑定模块必须主动检查值是否已更改。
   这是在 `refresh_loop()` 中完成的，该循环每 0.1 秒运行一次。
   该间隔可以通过 `ui.run()` 中的 `binding_refresh_interval` 进行配置。

“可绑定属性”非常高效，只要值不发生更改，就不会产生任何成本。
但是“活动链接”需要每秒检查 10 次所有绑定的值。
这可能会变得很昂贵，尤其是在您绑定到列表或字典等复杂对象时。

因为不长时间阻塞主线程至关重要，
如果 `refresh_loop()` 的一个步骤花费的时间太长，我们将显示警告。
您可以通过 `binding.MAX_PROPAGATION_TIME` 配置阈值，默认为 0.01 秒。
但通常，该警告是性能或内存问题的有价值的指标。
如果您的 CPU 忙于更新绑定很长时间，
主线程上将无法进行任何其他操作，并且 UI 会“挂起”。

以下演示展示了如何像在第一个演示中那样为 `Demo` 类定义和使用可绑定属性。
现在，`number` 属性是一个 `BindableProperty`，
这允许 NiceGUI 检测写入访问并立即触发值传播。

```python
from nicegui import binding, ui

class Demo:
    number = binding.BindableProperty()

    def __init__(self):
        self.number = 1

demo = Demo()
ui.slider(min=1, max=3).bind_value(demo, 'number')
ui.toggle({1: 'A', 2: 'B', 3: 'C'}).bind_value(demo, 'number')
ui.number(min=1, max=3).bind_value(demo, 'number')

ui.run()
```
