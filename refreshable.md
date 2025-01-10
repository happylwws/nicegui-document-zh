# ui. *refreshable*

## Refreshable UI functions

`@ui.refreshable` 装饰器允许您创建具有 `refresh` 方法的函数。此方法将自动删除该函数创建的所有元素并重新创建它们。

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

## Refreshable UI with parameters

这里演示了如何使用可刷新装饰器创建一个可以使用不同参数刷新的 UI。

```python
import pytz
from datetime import datetime
from nicegui import ui

@ui.refreshable
def clock_ui(timezone: str):
    ui.label(f'Current time in {timezone}:')
    ui.label(datetime.now(tz=pytz.timezone(timezone)).strftime('%H:%M:%S'))

clock_ui('Europe/Berlin')
ui.button('Refresh', on_click=clock_ui.refresh)
ui.button('Refresh for New York', on_click=lambda: clock_ui.refresh('America/New_York'))
ui.button('Refresh for Tokyo', on_click=lambda: clock_ui.refresh('Asia/Tokyo'))

ui.run()
```

## Refreshable UI for input validation

这里演示了如何使用可刷新装饰器来提供关于用户输入有效性的反馈。

```python
import re
from nicegui import ui

pwd = ui.input('Password', password=True, on_change=lambda: show_info.refresh())

rules = {
    'Lowercase letter': lambda s: re.search(r'[a-z]', s),
    'Uppercase letter': lambda s: re.search(r'[A-Z]', s),
    'Digit': lambda s: re.search(r'\d', s),
    'Special character': lambda s: re.search(r"[!@#$%^&*(),.?':{}|<>]", s),
    'min. 8 characters': lambda s: len(s) >= 8,
}

@ui.refreshable
def show_info():
    for rule, check in rules.items():
        with ui.row().classes('items-center gap-2'):
            if check(pwd.value or ''):
                ui.icon('done', color='green')
                ui.label(rule).classes('text-xs text-green strike-through')
            else:
                ui.icon('radio_button_unchecked', color='red')
                ui.label(rule).classes('text-xs text-red')

show_info()

ui.run()
```

## Refreshable UI with reactive state

您可以使用 `ui.state` 函数创建响应式状态变量，如本演示中的 `count` 和 `color`。它们可以像普通变量一样用于创建 UI 元素，如 `ui.label`。它们对应的 setter 函数可用于设置新值，这将自动刷新 UI。

```python
from nicegui import ui

@ui.refreshable
def counter(name: str):
    with ui.card():
        count, set_count = ui.state(0)
        color, set_color = ui.state('black')
        ui.label(f'{name} = {count}').classes(f'text-{color}')
        ui.button(f'{name} += 1', on_click=lambda: set_count(count + 1))
        ui.select(['black', 'red', 'green', 'blue'],
                  value=color, on_change=lambda e: set_color(e.value))

with ui.row():
    counter('A')
    counter('B')

ui.run()
```

## Global scope

当在全局范围内定义可刷新函数时，每次调用此函数创建的可刷新 UI 都将共享相同的状态。在此演示中，`time()` 将显示当前的日期和时间。当在新标签页中打开页面时，单击“刷新”按钮时，*两个*标签页将同时更新。

请参阅下面的“局部范围”演示，了解如何创建独立的可刷新 UI。

```python
from datetime import datetime
from nicegui import ui

@ui.refreshable
def time():
    ui.label(f'Time: {datetime.now()}')

@ui.page('/global_refreshable')
def demo():
    time()
    ui.button('Refresh', on_click=time.refresh)

ui.link('Open demo', demo)

ui.run()
```

## Local scope (variant A)

当在局部范围内定义可刷新函数时，通过调用此函数创建的可刷新 UI 将独立刷新。与“全局范围”演示相反，“刷新”按钮仅会在点击的选项卡中更新时间。

```python
from datetime import datetime
from nicegui import ui

@ui.page('/local_refreshable_a')
def demo():
    @ui.refreshable
    def time():
        ui.label(f'Time: {datetime.now()}')

    time()
    ui.button('Refresh', on_click=time.refresh)

ui.link('Open demo', demo)

ui.run()
```

## Local scope (variant B)

为了在页面函数之外定义具有局部状态的可刷新 UI，您可以定义一个具有可刷新方法的类。这样，您可以创建具有独立状态的类的多个实例，因为 `ui.refreshable` 装饰器作用于类实例而不是类本身。

```python
from datetime import datetime
from nicegui import ui

class Clock:
    @ui.refreshable
    def time(self):
        ui.label(f'Time: {datetime.now()}')

@ui.page('/local_refreshable_b')
def demo():
    clock = Clock()
    clock.time()
    ui.button('Refresh', on_click=clock.time.refresh)

ui.link('Open demo', demo)

ui.run()
```

## Local scope (variant C)

作为上面显示的类定义的替代方法，您也可以在全局范围内定义 UI 函数，但在页面函数内部应用 `ui.refreshable` 装饰器。这样，可刷新的 UI 将独立刷新。

```python
from datetime import datetime
from nicegui import ui

def time():
    ui.label(f'Time: {datetime.now()}')

@ui.page('/local_refreshable_c')
def demo():
    refreshable_time = ui.refreshable(time)
    refreshable_time()
    ui.button('Refresh', on_click=refreshable_time.refresh)

ui.link('Open demo', demo)

ui.run()
```

## Reference

### Methods

**`prune`**`() -> None`

删除所有不再位于具有客户端连接的页面上的目标。

此方法在每次刷新之前自动调用。

**`refresh`**`(*args: Any, kwargs: Any) -> None`

刷新此函数创建的 UI 元素。

此方法接受与函数本身相同的参数或它们的子集。它将传递给函数的参数与传递给此方法的参数组合起来。
