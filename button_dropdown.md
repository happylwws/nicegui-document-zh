# ui.dropdown_button

## Dropdown Button

这个元素基于 Quasar 的 [QBtnDropDown](https://quasar.dev/vue-components/button-dropdown) 组件。

`color` 参数接受一个 Quasar 颜色、一个 Tailwind 颜色或一个 CSS 颜色。
如果使用 Quasar 颜色，按钮将根据 Quasar 主题进行样式设置，包括文本颜色。
请注意，有些颜色（如“red”）既是 Quasar 颜色又是 CSS 颜色。
在这种情况下，将使用 Quasar 颜色。

| 参数 | 描述 |
| --- | --- |
| text: | 按钮的标签 |
| value: | 下拉列表是否打开 (默认: `False`) |
| on_value_change: | 当下拉列表打开或关闭时调用的回调函数 |
| on_click: | 当按钮被按下时调用的回调函数 |
| color: | 按钮的颜色 (Quasar, Tailwind, 或 CSS 颜色，或 `None`，默认: 'primary') |
| icon: | 要在按钮上显示的图标的名称 (默认: `None`) |
| auto_close: | 当点击一个项目时，下拉列表是否应该自动关闭 (默认: `False`) |
| split: | 是否将下拉图标拆分为一个单独的按钮 (默认: `False`) |

```python
from nicegui import ui

with ui.dropdown_button('Open me!', auto_close=True):
    ui.item('Item 1', on_click=lambda: ui.notify('You clicked item 1'))
    ui.item('Item 2', on_click=lambda: ui.notify('You clicked item 2'))

ui.run()
```

## Custom elements inside dropdown button

你可以在下拉按钮中放置任何元素。
这是一个包含几个开关的演示。

```python
from nicegui import ui

with ui.dropdown_button('Settings', icon='settings', split=True):
    with ui.row().classes('p-4 items-center'):
        ui.icon('volume_up', size='sm')
        ui.switch().props('color=negative')
        ui.separator().props('vertical')
        ui.icon('mic', size='sm')
        ui.switch().props('color=negative')

ui.run()
```

## Reference

## Initializer

| 参数 | 描述 |
| --- | --- |
| text: | 按钮的标签 |
| value: | 下拉列表是否打开 (默认: `False`) |
| on_value_change: | 当下拉列表打开或关闭时调用的回调函数 |
| on_click: | 当按钮被按下时调用的回调函数 |
| color: | 按钮的颜色 (Quasar, Tailwind, 或 CSS 颜色，或 `None`，默认: 'primary') |
| icon: | 要在按钮上显示的图标的名称 (默认: `None`) |
| auto_close: | 当点击一个项目时，下拉列表是否应该自动关闭 (默认: `False`) |
| split: | 是否将下拉图标拆分为一个单独的按钮 (默认: `False`) |

## Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`enabled`**`: BindableProperty`

**`icon`**`: BindableProperty`

**`is_deleted`**`: 'bool'`

指示元素是否已被删除。

**`is_ignoring_events`**`: bool`

返回元素当前是否忽略事件。

**`props`**`: 'Props[Self]'`

元素的属性。

**`style`**`: 'Style[Self]'`

元素的样式。

**`text`**`: BindableProperty`

**`value`**`: BindableProperty`

**`visible`**`: BindableProperty`

## Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

将资源添加到元素。

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
| include_self: | 是否在迭代中包含元素本身 |

**`bind_enabled`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的启用状态绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
反向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_enabled_from`**`(target_object: Any, target_name: str = 'enabled', backward: Callable[..., Any] = [...]) -> Self`

从此元素的启用状态绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标到该元素。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_enabled_to`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...]) -> Self`

将此元素的启用状态绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |

**`bind_icon`**`(target_object: Any, target_name: str = 'icon', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的图标绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
反向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_icon_from`**`(target_object: Any, target_name: str = 'icon', backward: Callable[..., Any] = [...]) -> Self`

从此元素的图标绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标到该元素。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_icon_to`**`(target_object: Any, target_name: str = 'icon', forward: Callable[..., Any] = [...]) -> Self`

将此元素的图标绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |

**`bind_text`**`(target_object: Any, target_name: str = 'text', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的文本绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
反向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_text_from`**`(target_object: Any, target_name: str = 'text', backward: Callable[..., Any] = [...]) -> Self`

从此元素的文本绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标到该元素。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_text_to`**`(target_object: Any, target_name: str = 'text', forward: Callable[..., Any] = [...]) -> Self`

将此元素的文本绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |

**`bind_value`**`(target_object: Any, target_name: str = 'value', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的值绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
反向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_value_from`**`(target_object: Any, target_name: str = 'value', backward: Callable[..., Any] = [...]) -> Self`

从此元素的值绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标到该元素。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_value_to`**`(target_object: Any, target_name: str = 'value', forward: Callable[..., Any] = [...]) -> Self`

将此元素的值绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
反向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标到该元素。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`close`**`() -> None`

关闭下拉列表。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的类字符串 |
| remove: | 要从元素中删除的以空格分隔的类字符串 |
| toggle: | 要切换的以空格分隔的类字符串 |
| replace: | 要使用的类字符串，而不是现有的类字符串，以空格分隔 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或其布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些属性。
这些必须在元素实例化之前定义。

如果未指定值，则布尔属性假定为 `True`。

|  参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的布尔值或键值对列表，以添加 |
| remove: | 要删除的属性键的以空格分隔的列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。
此类的所有元素将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加到元素的分号分隔的样式列表 |
| remove: | 要从元素中删除的分号分隔的样式列表 |
| replace: | 要使用的样式列表，而不是现有的样式列表，以分号分隔 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`disable`**`() -> None`

禁用该元素。

**`enable`**`() -> None`

启用该元素。

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回计算属性。

应等待此函数，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于使用 [ElementFilter](/documentation/element_filter) 识别元素，
这在测试中大量使用，
但也可用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或以空格分隔的标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认值：父容器） |
| target_index: | 目标插槽中的索引（默认值：追加到末尾） |
| target_slot: | 目标容器中的插槽（默认值：默认插槽） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅一个事件。

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如，“click”、“mousedown”或“update:model-value”） |
| handler: | 事件发生时调用的回调 |
| args: | 事件消息中包含的参数，发送到事件处理程序（默认值：`None`，表示全部） |
| throttle: | 事件发生之间的最短时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`on_value_change`**`(callback: Union[Callable[[nicegui.events.ValueChangeEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个在值更改时调用的回调。

**`open`**`() -> None`

打开下拉列表。

**`remove`**`(element: Union[Element, int]) -> None`

删除子元素。

|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果等待该函数，则返回方法调用的结果。
否则，该方法将执行而不等待响应。

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 传递给方法的参数 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`set_enabled`**`(value: bool) -> None`

设置元素的启用状态。

**`set_icon`**`(icon: Optional[str]) -> None`

设置此元素的图标。

|  参数 | 描述 |
| --- | --- |
| icon: | 新的图标。 |

**`set_text`**`(text: str) -> None`

设置此元素的文本。

|  参数 | 描述 |
| --- | --- |
| text: | 新的文本。 |

**`set_value`**`(value: Any) -> None`

设置此元素的值。

|  参数 | 描述 |
| --- | --- |
| value: | 要设置的值。 |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

|  参数 | 描述 |
| --- | --- |
| visible: | 元素是否应可见。 |

**`toggle`**`() -> None`

切换下拉列表。

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

|  参数 | 描述 |
| --- | --- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

在客户端更新元素。

## Inheritance

* `IconElement`
* `TextElement`
* `DisableableElement`
* `BackgroundColorElement`
* `ValueElement`
* `Element`
* `Visibility`
