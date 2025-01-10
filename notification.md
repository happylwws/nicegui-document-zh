# ui.notification

## Notification element

在屏幕上显示通知。
与 `ui.notify` 不同，此元素允许在通知显示后更新通知消息和其他属性。
可以使用 `dismiss()` 删除通知。

| 参数 | 描述 |
| --- | --- |
| message: | 通知的内容 |
| position: | 屏幕上的位置（"top-left"、"top-right"、"bottom-left"、"bottom-right"、"top"、"bottom"、"left"、"right" 或 "center"，默认为 "bottom"） |
| close_button: | 用于关闭通知的可选按钮标签（默认：`False`） |
| type: | 可选类型（"positive"、"negative"、"warning"、"info" 或 "ongoing"） |
| color: | 可选颜色名称 |
| multi_line: | 启用多行通知 |
| icon: | 要在通知中显示的可选图标名称（默认：`None`） |
| spinner: | 在通知中显示加载动画（默认：False） |
| timeout: | 可选的超时时间（以秒为单位），在此之后通知将被关闭（默认：5.0） |
| on_dismiss: | 关闭通知时要调用的可选回调 |
| options: | 包含所有选项的可选字典（覆盖所有其他参数） |

注意：您可以根据 [Quasar 的 Notify API](https://quasar.dev/quasar-plugins/notify#notify-api) 传递其他关键字参数。

```python
import asyncio
from nicegui import ui

async def compute():
    n = ui.notification(timeout=None)
    for i in range(10):
        n.message = f'Computing {i/10:.0%}'
        n.spinner = True
        await asyncio.sleep(0.2)
    n.message = 'Done!'
    n.spinner = False
    await asyncio.sleep(1)
    n.dismiss()

ui.button('Compute', on_click=compute)

ui.run()
```

## Reference

### Initializer

| 参数 | 描述 |
| --- | --- |
| message: | 通知的内容 |
| position: | 屏幕上的位置（"top-left"、"top-right"、"bottom-left"、"bottom-right"、"top"、"bottom"、"left"、"right" 或 "center"，默认为 "bottom"） |
| close_button: | 用于关闭通知的可选按钮标签（默认：`False`） |
| type: | 可选类型（"positive"、"negative"、"warning"、"info" 或 "ongoing"） |
| color: | 可选颜色名称 |
| multi_line: | 启用多行通知 |
| icon: | 要在通知中显示的可选图标名称（默认：`None`） |
| spinner: | 在通知中显示加载动画（默认：False） |
| timeout: | 可选的超时时间（以秒为单位），在此之后通知将被关闭（默认：5.0） |
| on_dismiss: | 关闭通知时要调用的可选回调 |
| options: | 包含所有选项的可选字典（覆盖所有其他参数） |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`close_button`**`: Union[bool, str] (settable)`

通知是否有关闭按钮。

**`color`**`: Optional[str] (settable)`

通知的颜色。

**`icon`**`: Optional[str] (settable)`

通知的图标。

**`is_deleted`**`: 'bool'`

元素是否已被删除。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`message`**`: str (settable)`

消息文本。

**`multi_line`**`: bool (settable)`

通知是否为多行。

**`position`**`: Literal['top-left', 'top-right', 'bottom-left', 'bottom-right', 'top', 'bottom', 'left', 'right', 'center'] (settable)`

在屏幕上的位置。

**`props`**`: 'Props[Self]'`

元素的 props。

**`spinner`**`: bool (settable)`

通知是否为加载动画。

**`style`**`: 'Style[Self]'`

元素的样式。

**`type`**`: Optional[Literal['positive', 'negative', 'warning', 'info', 'ongoing']] (settable)`

通知的类型。

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
| include_self: | 是否在迭代中包含元素本身 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标对象，以及从目标对象到该元素。
更新会立即发生，并在值更改时发生。
对于初始同步，向后绑定优先。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标对象之前应用于该值的函数。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标对象到此元素。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从此元素到目标对象。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标对象之前应用于该值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的类字符串 |
| remove: | 要从元素中删除的以空格分隔的类字符串 |
| toggle: | 要切换的以空格分隔的类字符串 |
| replace: | 要使用的以空格分隔的类字符串，而不是现有的类 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些属性。
这些必须在元素实例化之前定义。

如果未指定值，则假定布尔属性为 `True`。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加的布尔值或键值对的以空格分隔的列表 |
| remove: | 要删除的属性键的以空格分隔的列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。
此类的所有元素将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加到元素的以分号分隔的样式列表 |
| remove: | 要从元素中删除的以分号分隔的样式列表 |
| replace: | 要使用的以分号分隔的样式列表，而不是现有的样式 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`dismiss`**`() -> None`

关闭通知。

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

应等待此函数，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于标识要使用 [ElementFilter](documentation/element_filter) 查询的元素，该过滤器在测试中大量使用，但也可用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认值：父容器） |
| target_index: | 目标插槽内的索引（默认值：追加到末尾） |
| target_slot: | 目标容器内的插槽（默认值：默认插槽） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如，“click”、“mousedown” 或 “update:model-value”） |
| handler: | 事件发生时调用的回调 |
| args: | 事件消息中包含的参数，发送到事件处理程序（默认值：`None`，表示所有参数） |
| throttle: | 事件发生之间的最短时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`on_dismiss`**`(callback: Union[Callable[[nicegui.events.UiEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个在关闭通知时调用的回调。

**`remove`**`(element: Union[Element, int]) -> None`

删除子元素。

|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果该函数被等待，则返回方法调用的结果。
否则，将执行该方法，而不等待响应。

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 传递给方法的参数 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`set_visibility`**`(visible: bool) -> None`

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

|  参数 | 描述 |
| --- | --- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

在客户端更新元素。

### Inheritance

* `Element`
* `Visibility`
