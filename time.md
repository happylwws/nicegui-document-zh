# ui.time

## Time Input

此元素基于 Quasar 的 [QTime](https://quasar.dev/vue-components/time) 组件。时间是一个字符串，格式由 `mask` 参数定义。

| 参数        | 描述                                                              |
| ----------- | ----------------------------------------------------------------- |
| value:      | 初始时间                                                            |
| mask:       | 时间字符串的格式 (默认: 'HH:mm')                                      |
| on_change: | 时间更改时执行的回调函数                                                  |

```python
from nicegui import ui

ui.time(value='12:00', on_change=lambda e: result.set_text(e.value))
result = ui.label()

ui.run()
```

## Input element with time picker

此演示展示了如何使用输入元素实现时间选择器。
我们在输入元素的 append slot 中放置一个图标。
当单击图标时，我们会打开一个带有时间选择器的菜单。
[QMenu](https://quasar.dev/vue-components/menu) 的 "no-parent-event" 属性用于
防止在点击输入字段时打开菜单。
由于菜单默认不带“关闭”按钮，我们为了方便添加了一个。

时间绑定到输入元素的值。
因此，无论何时更改时间，输入元素和时间选择器都将保持同步。

```python
from nicegui import ui

with ui.input('Time') as time:
    with ui.menu().props('no-parent-event') as menu:
        with ui.time().bind_value(time):
            with ui.row().classes('justify-end'):
                ui.button('Close', on_click=menu.close).props('flat')
    with time.add_slot('append'):
        ui.icon('access_time').on('click', menu.open).classes('cursor-pointer')

ui.run()
```

## Reference

### Initializer

| 参数        | 描述                                                              |
| ----------- | ----------------------------------------------------------------- |
| value:      | 初始时间                                                            |
| mask:       | 时间字符串的格式 (默认: 'HH:mm')                                      |
| on_change: | 时间更改时执行的回调函数                                                  |

### Properties

**`classes`**`: 'Classes[Self]'`

The classes of the element.

**`enabled`**`: BindableProperty`

**`is_deleted`**`: 'bool'`

Whether the element has been deleted.

**`is_ignoring_events`**`: bool`

Return whether the element is currently ignoring events.

**`props`**`: 'Props[Self]'`

The props of the element.

**`style`**`: 'Style[Self]'`

The style of the element.

**`value`**`: BindableProperty`

**`visible`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

Add a resource to the element.

| 参数   | 描述                                                                 |
| ------ | -------------------------------------------------------------------- |
| path: | 资源路径 (例如：包含 CSS 和 JavaScript 文件的文件夹)                                   |

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

Iterate over the ancestors of the element.

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素自身 |

**`bind_enabled`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

Bind the enabled state of this element to the target object's target_name property.

The binding works both ways, from this element to the target and from the target to this element.
The update happens immediately and whenever a value changes.
The backward binding takes precedence for the initial synchronization.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_enabled_from`**`(target_object: Any, target_name: str = 'enabled', backward: Callable[..., Any] = [...]) -> Self`

Bind the enabled state of this element from the target object's target_name property.

The binding works one way only, from the target to this element.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定来源的对象。 |
| target_name: | 要绑定来源的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_enabled_to`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...]) -> Self`

Bind the enabled state of this element to the target object's target_name property.

The binding works one way only, from this element to the target.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |

**`bind_value`**`(target_object: Any, target_name: str = 'value', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

Bind the value of this element to the target object's target_name property.

The binding works both ways, from this element to the target and from the target to this element.
The update happens immediately and whenever a value changes.
The backward binding takes precedence for the initial synchronization.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_value_from`**`(target_object: Any, target_name: str = 'value', backward: Callable[..., Any] = [...]) -> Self`

Bind the value of this element from the target object's target_name property.

The binding works one way only, from the target to this element.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定来源的对象。 |
| target_name: | 要绑定来源的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |

**`bind_value_to`**`(target_object: Any, target_name: str = 'value', forward: Callable[..., Any] = [...]) -> Self`

Bind the value of this element to the target object's target_name property.

The binding works one way only, from this element to the target.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

Bind the visibility of this element to the target object's target_name property.

The binding works both ways, from this element to the target and from the target to this element.
The update happens immediately and whenever a value changes.
The backward binding takes precedence for the initial synchronization.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

Bind the visibility of this element from the target object's target_name property.

The binding works one way only, from the target to this element.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定来源的对象。 |
| target_name: | 要绑定来源的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

Bind the visibility of this element to the target object's target_name property.

The binding works one way only, from this element to the target.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |

**`clear`**`() -> None`

Remove all child elements.

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

Apply, remove, toggle, or replace default HTML classes.

This allows modifying the look of the element or its layout using [Tailwind](https://tailwindcss.com/) or [Quasar](https://quasar.dev/) classes.

Removing or replacing classes can be helpful if predefined classes are not desired.
All elements of this class will share these HTML classes.
These must be defined before element instantiation.

|  参数 | 描述 |
| --- | --- |
| add: | 空格分隔的类字符串 |
| remove: | 要从元素中删除的空格分隔的类字符串 |
| toggle: | 要切换的空格分隔的类字符串 |
| replace: | 用于替换现有类的空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

Add or remove default props.

This allows modifying the look of the element or its layout using [Quasar](https://quasar.dev/) props.
Since props are simply applied as HTML attributes, they can be used with any HTML element.
All elements of this class will share these props.
These must be defined before element instantiation.

Boolean properties are assumed `True` if no value is specified.

|  参数 | 描述 |
| --- | --- |
| add: | 要添加的布尔值或键值对的空格分隔列表 |
| remove: | 要删除的属性键的空格分隔列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

Apply, remove, or replace default CSS definitions.

Removing or replacing styles can be helpful if the predefined style is not desired.
All elements of this class will share these CSS definitions.
These must be defined before element instantiation.

|  参数 | 描述 |
| --- | --- |
| add: | 要添加到元素的分号分隔的样式列表 |
| remove: | 要从元素中删除的分号分隔的样式列表 |
| replace: | 用于替换现有样式的分号分隔的样式列表 |

**`delete`**`() -> None`

Delete the element and all its children.

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

Iterate over the descendants of the element.

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素自身 |

**`disable`**`() -> None`

Disable the element.

**`enable`**`() -> None`

Enable the element.

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

Return a computed property.

This function should be awaited so that the computed property is properly returned.

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最大时间（默认：1 秒） |

**`mark`**`(*markers: str) -> Self`

Replace markers of the element.

Markers are used to identify elements for querying with [ElementFilter](/documentation/element_filter)
which is heavily used in testing
but can also be used to reduce the number of global variables or passing around dependencies.

|  参数 | 描述 |
| --- | --- |
| markers: | 带有空格分隔标记的字符串列表或单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

Move the element to another container.

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认：父容器） |
| target_index: | 目标插槽内的索引（默认：追加到末尾） |
| target_slot: | 目标容器内的插槽（默认：默认插槽） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

Subscribe to an event.

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称 (例如, "click", "mousedown", 或 "update:model-value") |
| handler: | 事件发生时调用的回调函数 |
| args: | 事件消息中包含的参数，发送给事件处理程序 (默认: `None` 表示全部) |
| throttle: | 事件发生之间的最短时间（以秒为单位）(默认: 0.0) |
| leading_events: | 是否在第一次事件发生后立即触发事件处理程序 (默认: `True`) |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序 (默认: `True`) |
| js_handler: | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)` (默认: `None`) |

**`on_value_change`**`(callback: Union[Callable[[nicegui.events.ValueChangeEventArguments], Any], Callable[[], Any]]) -> Self`

Add a callback to be invoked when the value changes.

**`remove`**`(element: Union[Element, int]) -> None`

Remove a child element.

|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

Run a method on the client side.

If the function is awaited, the result of the method call is returned.
Otherwise, the method is executed without waiting for a response.

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 传递给方法的参数 |
| timeout: | 等待响应的最大时间（默认：1 秒） |

**`set_enabled`**`(value: bool) -> None`

Set the enabled state of the element.

**`set_value`**`(value: Any) -> None`

Set the value of this element.

|  参数 | 描述      |
| ---- | --------- |
| value: | 要设置的值。 |

**`set_visibility`**`(visible: bool) -> None`

Set the visibility of this element.

|  参数 | 描述                    |
| ---- | ----------------------- |
| visible: | 元素是否应该可见。        |

**`tooltip`**`(text: str) -> Self`

Add a tooltip to the element.

|  参数 | 描述       |
| ---- | ---------- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

Update the element on the client side.

### Inheritance

* `ValueElement`
* `DisableableElement`
* `Element`
* `Visibility`
