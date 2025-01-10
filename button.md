# ui.button

## Button

此元素基于 Quasar 的 [QBtn](https://quasar.dev/vue-components/button) 组件。

`color` 参数接受 Quasar 颜色、Tailwind 颜色或 CSS 颜色。
如果使用 Quasar 颜色，按钮将根据 Quasar 主题进行样式设置，包括文本颜色。
请注意，有些颜色如 "red" 既是 Quasar 颜色又是 CSS 颜色。
在这种情况下，将使用 Quasar 颜色。

| 参数 | 描述 |
| --- | --- |
| text: | 按钮的标签 |
| on_click: | 按下按钮时调用的回调函数 |
| color: | 按钮的颜色（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认值: 'primary'） |
| icon: | 要在按钮上显示的图标名称（默认值: `None`） |

```python
from nicegui import ui

ui.button('Click me!', on_click=lambda: ui.notify('You clicked me!'))

ui.run()
```

## Icons

您还可以向按钮添加图标。

```python
from nicegui import ui

with ui.row():
    ui.button('demo', icon='history')
    ui.button(icon='thumb_up')
    with ui.button():
        ui.label('sub-elements')
        ui.image('https://picsum.photos/id/377/640/360') \
            .classes('rounded-full w-16 h-16 ml-4')

ui.run()
```

## Await button click

有时，在继续执行之前等待按钮点击会很方便。

```python
from nicegui import ui

@ui.page('/')
async def index():
    b = ui.button('Step')
    await b.clicked()
    ui.label('One')
    await b.clicked()
    ui.label('Two')
    await b.clicked()
    ui.label('Three')

ui.run()
```

## Disable button with a context manager

这展示了一个上下文管理器，可用于在异步过程期间禁用按钮。

```python
import httpx
from contextlib import contextmanager
from nicegui import ui

@contextmanager
def disable(button: ui.button):
    button.disable()
    try:
        yield
    finally:
        button.enable()

async def get_slow_response(button: ui.button) -> None:
    with disable(button):
        async with httpx.AsyncClient() as client:
            response = await client.get('https://httpbin.org/delay/1', timeout=5)
            ui.notify(f'Response code: {response.status_code}')

ui.button('Get slow response', on_click=lambda e: get_slow_response(e.sender))

ui.run()
```

## Custom toggle button

与其他所有元素一样，您可以实现具有专门逻辑的自定义子类。
例如，这个带有内部布尔状态的红/绿切换按钮。

```python
from nicegui import ui

class ToggleButton(ui.button):

    def __init__(self, *args, **kwargs) -> None:
        super().__init__(*args, **kwargs)
        self._state = False
        self.on('click', self.toggle)

    def toggle(self) -> None:
        """Toggle the button state."""
        self._state = not self._state
        self.update()

    def update(self) -> None:
        self.props(f'color={"green" if self._state else "red"}')
        super().update()

ToggleButton('Toggle me')

ui.run()
```

## Floating Action Button

正如 [Quasar 文档](https://quasar.dev/vue-components/floating-action-button) 中所述，
浮动操作按钮 (FAB) 只是一个内部带有按钮的 "page-sticky"。
使用 "fab" prop，按钮将变为圆形并带有阴影。
颜色可以自由选择，但最常见的是强调色。

```python
from nicegui import ui

ui.colors(accent='#6AD4DD')
with ui.page_sticky(x_offset=18, y_offset=18):
    ui.button(icon='home', on_click=lambda: ui.notify('home')) \
        .props('fab color=accent')

ui.run()
```

## Expandable Floating Action Button

[Quasar FAB (q-fab)](https://quasar.dev/vue-components/floating-action-button)，
是一个在点击时显示多个操作的按钮。
虽然它在 NiceGUI 中不是一个单独的元素，但可以使用通用的 `ui.element` 轻松创建。

```python
from nicegui import ui

with ui.element('q-fab').props('icon=navigation color=green'):
    ui.element('q-fab-action').props('icon=train color=green-5') \
        .on('click', lambda: ui.notify('train'))
    ui.element('q-fab-action').props('icon=sailing color=green-5') \
        .on('click', lambda: ui.notify('boat'))
    ui.element('q-fab-action').props('icon=rocket color=green-5') \
        .on('click', lambda: ui.notify('rocket'))

ui.run()
```

## Reference

### Initializer

| 参数 | 描述 |
| --- | --- |
| text: | 按钮的标签 |
| on_click: | 按下按钮时调用的回调函数 |
| color: | 按钮的颜色（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认值: 'primary'） |
| icon: | 要在按钮上显示的图标名称（默认值: `None`） |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`enabled`**`: BindableProperty`

**`icon`**`: BindableProperty`

**`is_deleted`**`: 'bool'`

指示元素是否已被删除。

**`is_ignoring_events`**`: bool`

返回元素当前是否忽略事件。

**`props`**`: 'Props[Self]'`

元素的 props。

**`style`**`: 'Style[Self]'`

元素的样式。

**`text`**`: BindableProperty`

**`visible`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

向元素添加资源。

|  参数 | 描述 |
| --- | --- |
| path: | 资源路径 (例如：包含 CSS 和 JavaScript 文件的文件夹) |

**`add_slot`**`(name: str, template: Optional[str] = None) -> Slot`

向元素添加一个slot.

NiceGUI 使用 Vue 中的 slot 概念：
元素可以有多个 slot，每个 slot 可能都有多个子元素。
大多数元素只有一个 slot，例如 `ui.card` (QCard) 只有一个默认 slot。
但是，更复杂的元素（如 `ui.table` (QTable)）可以有更多 slot，例如 “header”、“body” 等。
如果使用 `ui.row(): ...` 通过 with 嵌套 NiceGUI 元素，则将新元素放置在行的默认 slot 中。
但是，如果使用 with `table.add_slot(...): ...`，则会进入不同的 slot。

slot 堆栈可帮助 NiceGUI 跟踪当前哪个 slot 用于新元素。
`parent` 字段保存对其元素的引用。
每当通过 with 表达式进入元素时，其默认 slot 也会自动进入。

|  参数 | 描述 |
| --- | --- |
| name: | slot 的名称 |
| template: | slot 的 Vue 模板 |
| return: | slot |

**`ancestors`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的祖先。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包括元素本身 |

**`bind_enabled`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的启用状态绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在每次值更改时发生。
向后绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_enabled_from`**`(target_object: Any, target_name: str = 'enabled', backward: Callable[..., Any] = [...]) -> Self`

从此元素的启用状态从目标对象的 target_name 属性绑定。

绑定仅单向起作用，从目标到此元素。
更新会立即发生，并在每次值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_enabled_to`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...]) -> Self`

将此元素的启用状态绑定到目标对象的 target_name 属性。

绑定仅单向起作用，从该元素到目标。
更新会立即发生，并在每次值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |

**`bind_icon`**`(target_object: Any, target_name: str = 'icon', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的图标绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在每次值更改时发生。
向后绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_icon_from`**`(target_object: Any, target_name: str = 'icon', backward: Callable[..., Any] = [...]) -> Self`

将此元素的图标从此元素绑定到目标对象的 target_name 属性。

绑定仅单向起作用，从目标到此元素。
更新会立即发生，并在每次值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_icon_to`**`(target_object: Any, target_name: str = 'icon', forward: Callable[..., Any] = [...]) -> Self`

将此元素的图标绑定到目标对象的 target_name 属性。

绑定仅单向起作用，从该元素到目标。
更新会立即发生，并在每次值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |

**`bind_text`**`(target_object: Any, target_name: str = 'text', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的文本绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在每次值更改时发生。
向后绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_text_from`**`(target_object: Any, target_name: str = 'text', backward: Callable[..., Any] = [...]) -> Self`

将此元素的文本从此元素绑定到目标对象的 target_name 属性。

绑定仅单向起作用，从目标到此元素。
更新会立即发生，并在每次值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_text_to`**`(target_object: Any, target_name: str = 'text', forward: Callable[..., Any] = [...]) -> Self`

将此元素的文本绑定到目标对象的 target_name 属性。

绑定仅单向起作用，从该元素到目标。
更新会立即发生，并在每次值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在每次值更改时发生。
向后绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此元素的可见性从目标对象的 target_name 属性绑定。

绑定仅单向起作用，从目标到此元素。
更新会立即发生，并在每次值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅单向起作用，从该元素到目标。
更新会立即发生，并在每次值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`clicked`**`() -> None`

等待直到按钮被点击。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 空格分隔的类字符串 |
| remove: | 要从元素中删除的空格分隔的类字符串 |
| toggle: | 要切换的空格分隔的类字符串 |
| replace: | 用于代替现有类的空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认 props。

这允许使用 [Quasar](https://quasar.dev/) props 修改元素的外观或布局。
由于 props 只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些 props。
这些必须在元素实例化之前定义。

如果未指定值，则布尔属性假定为 `True`。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加的布尔值或键值对的空格分隔列表 |
| remove: | 要删除的属性键的空格分隔列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。
此类的所有元素将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加到元素的分号分隔的样式列表 |
| remove: | 要从元素中删除的分号分隔的样式列表 |
| replace: | 用于代替现有样式的分号分隔的样式列表 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包括元素本身 |

**`disable`**`() -> None`

禁用元素。

**`enable`**`() -> None`

启用元素。

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

应等待此函数，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于使用 [ElementFilter](https://nicegui.io/documentation/element_filter) 查询元素，
这在测试中大量使用，
但也可用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认值：父容器） |
| target_index: | 目标 slot 中的索引（默认值：追加到末尾） |
| target_slot: | 目标容器中的 slot（默认值：默认 slot） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如，“click”、“mousedown”或“update:model-value”） |
| handler: | 事件发生时调用的回调函数 |
| args: |  包含在发送到事件处理程序的事件消息中的参数（默认值：`None` 表示全部） |
| throttle: | 事件发生之间的最小时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)` (默认值: `None`) |

**`on_click`**`(callback: Union[Callable[[nicegui.events.ClickEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个在单击按钮时调用的回调函数。

**`remove`**`(element: Union[Element, int]) -> None`

删除子元素。

|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果等待该函数，则返回方法调用的结果。
否则，将执行该方法，而不等待响应。

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 要传递给方法的参数 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`set_enabled`**`(value: bool) -> None`

设置元素的启用状态。

**`set_icon`**`(icon: Optional[str]) -> None`

设置此元素的图标。

|  参数 | 描述 |
| --- | --- |
| icon: | 新图标。 |

**`set_text`**`(text: str) -> None`

设置此元素的文本。

|  参数 | 描述 |
| --- | --- |
| text: | 新文本。 |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

|  参数 | 描述 |
| --- | --- |
| visible: | 元素是否应该可见。 |

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

|  参数 | 描述 |
| --- | --- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

在客户端更新元素。

### Inheritance

*   `IconElement`
*   `TextElement`
*   `DisableableElement`
*   `BackgroundColorElement`
*  `Element`
*   `Visibility`
