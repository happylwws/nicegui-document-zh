# app.Timer

## Timer

创建 NiceGUI 的一个主要驱动力是需要一种简单的方法来定期更新界面，例如显示带有传入测量值的图形。
定时器将以给定的间隔重复执行回调。

| 选项       | 描述                                                                         |
| ---------- | ---------------------------------------------------------------------------- |
| interval:  | 定时器被调用的间隔（可以在运行时更改）                                         |
| callback:  | 间隔过去后要执行的函数或协程                                                 |
| active:    | 是否应该执行回调（可以在运行时更改）                                         |
| once:      | 回调是否仅在由 `interval` 指定的延迟后执行一次（默认值：`False`）          |
| immediate: | 是否应立即执行回调（默认值：`True`，如果 `once` 为 `True`，则忽略）           |

```python
from datetime import datetime
from nicegui import ui

label = ui.label()
ui.timer(1.0, lambda: label.set_text(f'{datetime.now():%X}'))

ui.run()
```

## Activate, deactivate and cancel a timer

您可以使用 `active` 属性激活和停用定时器。
您可以使用 `cancel` 方法取消定时器。
取消定时器后，它不能再被激活。

```python
from nicegui import ui

slider = ui.slider(min=0, max=1, value=0.5)
timer = ui.timer(0.1, lambda: slider.set_value((slider.value + 0.01) % 1.0))
ui.switch('active').bind_value_to(timer, 'active')
ui.button('Cancel', on_click=timer.cancel)

ui.run()
```

## Call a function after a delay

您可以使用带有 `once` 参数的定时器在延迟后调用函数。

```python
from nicegui import ui

def handle_click():
    ui.timer(1.0, lambda: ui.notify('Hi!'), once=True)
ui.button('Notify after 1 second', on_click=handle_click)

ui.run()
```

## Don't start immediately

默认情况下，定时器将立即启动。
您可以通过将 `immediate` 参数设置为 `False` 来更改此行为。
这将把回调的第一次执行延迟给定的间隔。

```python
from datetime import datetime
from nicegui import ui

label = ui.label()
ui.timer(1.0, lambda: label.set_text(f'{datetime.now():%X}'), immediate=False)

ui.run()
```

## Global app timer

虽然 `ui.timer` 是一种在当前页面上下文中运行的 UI 元素，但您也可以对独立于 UI 的计时器使用全局 `app.timer`。

```python
from nicegui import app, ui

counter = {'value': 0}
app.timer(1.0, lambda: counter.update(value=counter['value'] + 1))

@ui.page('/')
def page():
    ui.label().bind_text_from(counter, 'value', lambda value: f'Count: {value}')

ui.run()
```

## Reference

### Initializer

| 参数        | 描述                                                                         |
| ----------- | ---------------------------------------------------------------------------- |
| interval:   | 定时器被调用的间隔（可以在运行时更改）                                         |
| callback:   | 间隔过去后要执行的函数或协程                                                 |
| active:     | 是否应该执行回调（可以在运行时更改）                                         |
| once:       | 回调是否仅在由 `interval` 指定的延迟后执行一次（默认值：`False`）          |
| immediate:  | 是否应立即执行回调（默认值：`True`，如果 `once` 为 `True`，则忽略）           |

### Properties

**`active`**`: BindableProperty`

**`classes`**`: 'Classes[Self]'`

The classes of the element.

**`interval`**`: BindableProperty`

**`is_deleted`**`: 'bool'`

Whether the element has been deleted.

**`is_ignoring_events`**`: 'bool'`

Return whether the element is currently ignoring events.

**`props`**`: 'Props[Self]'`

The props of the element.

**`style`**`: 'Style[Self]'`

The style of the element.

**`visible`**`: BindableProperty`

### Methods

**`activate`**`() -> None`

Activate the timer.

**`add_resource`**`(path: Union[str, Path]) -> None`

Add a resource to the element.

| 参数     | 描述                                            |
| -------- | ----------------------------------------------- |
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

|  参数     | 描述           |
| -------- | ------------- |
| name:    | slot 的名称    |
| template: | slot 的 Vue 模板 |
| return:  | slot         |

**`ancestors`**`(include_self: bool = False) -> Iterator[Element]`

Iterate over the ancestors of the element.

|  参数          | 描述                                 |
| ------------- | ------------------------------------ |
| include_self: | 是否在迭代中包含元素本身 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

Bind the visibility of this element to the target object's target_name property.

The binding works both ways, from this element to the target and from the target to this element.
The update happens immediately and whenever a value changes.
The backward binding takes precedence for the initial synchronization.

|  参数           | 描述                                                                |
| -------------- | ------------------------------------------------------------------- |
| target_object:  | 要绑定到的对象。                                                   |
| target_name:   | 要绑定到的属性的名称。                                              |
| forward:       | 一个函数，用于在将值应用于目标之前对其进行应用。                |
| backward:      | 一个函数，用于在将值应用于此元素之前对其进行应用。           |
| value:         | 如果指定，则仅当目标值等于此值时，该元素才可见。          |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

Bind the visibility of this element from the target object's target_name property.

The binding works one way only, from the target to this element.
The update happens immediately and whenever a value changes.

|  参数           | 描述                                                                |
| -------------- | ------------------------------------------------------------------- |
| target_object:  | 要绑定到的对象。                                                    |
| target_name:   | 要绑定到的属性的名称。                                              |
| backward:      | 一个函数，用于在将值应用于此元素之前对其进行应用。           |
| value:         | 如果指定，则仅当目标值等于此值时，该元素才可见。          |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

Bind the visibility of this element to the target object's target_name property.

The binding works one way only, from this element to the target.
The update happens immediately and whenever a value changes.

|  参数           | 描述                                                                |
| -------------- | ------------------------------------------------------------------- |
| target_object:  | 要绑定到的对象。                                                   |
| target_name:   | 要绑定到的属性的名称。                                              |
| forward:       | 一个函数，用于在将值应用于目标之前对其进行应用。                |

**`cancel`**`() -> None`

Cancel the timer.

**`clear`**`() -> None`

Remove all child elements.

**`deactivate`**`() -> None`

Deactivate the timer.

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

Apply, remove, toggle, or replace default HTML classes.

This allows modifying the look of the element or its layout using [Tailwind](https://tailwindcss.com/) or [Quasar](https://quasar.dev/) classes.

Removing or replacing classes can be helpful if predefined classes are not desired.
All elements of this class will share these HTML classes.
These must be defined before element instantiation.

|  参数      | 描述                                                   |
| -------- | ---------------------------------------------------- |
| add:    | 空格分隔的类字符串                                       |
| remove: | 空格分隔的要从元素中删除的类字符串                       |
| toggle: | 空格分隔的要切换的类字符串                              |
| replace: | 空格分隔的要用来替换现有类的类字符串                     |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

Add or remove default props.

This allows modifying the look of the element or its layout using [Quasar](https://quasar.dev/) props.
Since props are simply applied as HTML attributes, they can be used with any HTML element.
All elements of this class will share these props.
These must be defined before element instantiation.

Boolean properties are assumed `True` if no value is specified.

|  参数      | 描述                                                   |
| -------- | ---------------------------------------------------- |
| add:    | 空格分隔的要添加的布尔值或 key=value 对列表         |
| remove: | 空格分隔的要删除的属性键列表                               |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

Apply, remove, or replace default CSS definitions.

Removing or replacing styles can be helpful if the predefined style is not desired.
All elements of this class will share these CSS definitions.
These must be defined before element instantiation.

|  参数      | 描述                                                   |
| -------- | ---------------------------------------------------- |
| add:    | 分号分隔的要添加到元素的样式列表                       |
| remove: | 分号分隔的要从元素中删除的样式列表                    |
| replace: | 分号分隔的要用来替换现有样式的样式列表                 |

**`delete`**`() -> None`

Delete the element and all its children.

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

Iterate over the descendants of the element.

|  参数          | 描述                                 |
| ------------- | ------------------------------------ |
| include_self: | 是否在迭代中包含元素本身 |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

Return a computed property.

This function should be awaited so that the computed property is properly returned.

|  参数        | 描述                                         |
| ----------- | -------------------------------------------- |
| prop_name:  | 计算属性的名称                                   |
| timeout:    | 等待响应的最大时间（默认值：1 秒）                |

**`mark`**`(*markers: str) -> Self`

Replace markers of the element.

Markers are used to identify elements for querying with [ElementFilter](/documentation/element_filter)
which is heavily used in testing
but can also be used to reduce the number of global variables or passing around dependencies.

|  参数      | 描述                                                                             |
| -------- | ------------------------------------------------------------------------------ |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

Move the element to another container.

|  参数              | 描述                                               |
| ----------------- | -------------------------------------------------- |
| target_container: | 要将元素移动到的容器（默认：父容器）           |
| target_index:    | 目标 slot 中的索引（默认：追加到末尾）              |
| target_slot:     | 目标容器中的 slot（默认：默认 slot）                  |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

Subscribe to an event.

|  参数            | 描述                                                                                              |
| --------------- | ------------------------------------------------------------------------------------------------- |
| type:          | 事件的名称（例如，“click”，“mousedown”或“update:model-value”）                                    |
| handler:       | 事件发生时调用的回调函数                                                                         |
| args:          | 发送到事件处理程序的消息中包含的参数（默认值：`None` 表示所有）                                 |
| throttle:      | 事件发生之间的最小时间（以秒为单位）（默认值：0.0）                                                    |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`）                                         |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`）                                           |
| js_handler:    | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`）                      |

**`remove`**`(element: Union[Element, int]) -> None`

Remove a child element.

|  参数      | 描述                  |
| -------- | -------------------- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

Run a method on the client side.

If the function is awaited, the result of the method call is returned.
Otherwise, the method is executed without waiting for a response.

|  参数       | 描述                                      |
| ---------- | ----------------------------------------- |
| name:      | 方法的名称                                  |
| args:      | 传递给方法的参数                             |
| timeout:  | 等待响应的最大时间（默认值：1 秒）             |

**`set_visibility`**`(visible: bool) -> None`

**`tooltip`**`(text: str) -> Self`

Add a tooltip to the element.

|  参数   | 描述            |
| ----- | -------------- |
| text: | tooltip 的文本 |

**`update`**`() -> None`

Update the element on the client side.

### Inheritance

*   `Timer`
*   `Element`
*   `Visibility`
