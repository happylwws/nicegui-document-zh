# ui.line_plot

## Line Plot

使用 pyplot 创建折线图。当与 `ui.timer` 结合使用时，`push` 方法可以提供实时更新。

| 选项 | 描述 |
| --- | --- |
| n: |  线的数量 |
| limit: | 每条线的数据点最大数量（新点将取代最旧的点） |
| update_every: |  仅在多次推送新数据后才更新图表，以节省 CPU 和带宽 |
| close: |  是否在退出上下文后关闭图形；如果您想稍后更新它，请设置为 `False` (默认: `True`) |
| kwargs: |  应传递给 [pyplot.figure](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.figure.html) 的参数，如 `figsize` |

```python
import math
from datetime import datetime
from nicegui import ui

line_plot = ui.line_plot(n=2, limit=20, figsize=(3, 2), update_every=5) \
    .with_legend(['sin', 'cos'], loc='upper center', ncol=2)

def update_line_plot() -> None:
    now = datetime.now()
    x = now.timestamp()
    y1 = math.sin(x)
    y2 = math.cos(x)
    line_plot.push([now], [[y1], [y2]])

line_updates = ui.timer(0.1, update_line_plot, active=False)
line_checkbox = ui.checkbox('active').bind_value(line_updates, 'active')

ui.run()
```

## Reference

## Initializer

|  参数 | 描述 |
| --- | --- |
| n: | 线的数量 |
| limit: |  每条线的数据点最大数量（新点将取代最旧的点） |
| update_every: | 仅在多次推送新数据后才更新图表，以节省 CPU 和带宽 |
| close: | 是否在退出上下文后关闭图形；如果您想稍后更新它，请设置为 `False` (默认: `True`) |
| kwargs: |  应传递给 [pyplot.figure](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.figure.html) 的参数，如 `figsize` |

## Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`is_deleted`**`: 'bool'`

元素是否已被删除。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`props`**`: 'Props[Self]'`

元素的 props。

**`style`**`: 'Style[Self]'`

元素的样式。

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

遍历元素的祖先。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值发生更改时发生。
对于初始同步，向后绑定优先。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: |  在将其应用于目标之前应用于该值的一个函数。 |
| backward: | 在将其应用于此元素之前应用于该值的一个函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从目标对象的 target_name 属性绑定此元素的可见性。

绑定仅以一种方式工作，从目标到此元素。
更新会立即发生，并在值发生更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的一个函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从该元素到目标。
更新会立即发生，并在值发生更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的一个函数。 |

**`clear`**`() -> None`

清除折线图。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的类字符串 |
| remove: | 从元素中删除的以空格分隔的类字符串 |
| toggle: | 要切换的以空格分隔的类字符串 |
| replace: |  要使用的类字符串，以空格分隔，而不是现有的类 |

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
| remove: |  要删除的属性键的空格分隔列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。
此类的所有元素将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: |  要添加到元素的以分号分隔的样式列表 |
| remove: |  要从元素中删除的以分号分隔的样式列表 |
| replace: | 要使用的样式，以分号分隔，而不是现有的样式 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

遍历元素的后代。

|  参数 | 描述 |
| --- | --- |
| include_self: |  是否在迭代中包含元素本身 |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

应该等待此函数，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: |  等待响应的最大时间（默认：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于标识使用 [ElementFilter](/documentation/element_filter) 查询的元素，该过滤器大量用于测试，但也可用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: |  带有以空格分隔的标记的字符串列表或单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: |  要将元素移动到的容器（默认：父容器） |
| target_index: |  目标 slot 中的索引（默认：追加到末尾） |
| target_slot: |  目标容器中的 slot（默认：默认 slot） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

|  参数 | 描述 |
| --- | --- |
| type: |  事件的名称（例如“click”、“mousedown”或“update:model-value”） |
| handler: |  在发生事件时调用的回调 |
| args: |  事件消息中包含的参数，该消息发送到事件处理程序（默认：`None` 表示全部） |
| throttle: | 事件发生之间的最短时间（以秒为单位）（默认值：0.0） |
| leading_events: |  是否在第一次事件发生后立即触发事件处理程序 (默认: `True`) |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认：`True`） |
| js_handler: |  在事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认：`None`） |

**`push`**`(x: List[float], Y: List[List[float]]) -> None`

将新数据推送到绘图。

|  参数 | 描述 |
| --- | --- |
| x: | x 值列表 |
| Y: | y 值列表的列表（每行一个列表） |

**`remove`**`(element: Union[Element, int]) -> None`

删除子元素。

|  参数 | 描述 |
| --- | --- |
| element: |  元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果该函数被等待，则返回方法调用的结果。
否则，将执行该方法，而无需等待响应。

|  参数 | 描述 |
| --- | --- |
| name: |  方法的名称 |
| args: | 传递给方法的参数 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

|  参数 | 描述 |
| --- | --- |
| visible: |  元素是否应该可见。 |

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

|  参数 | 描述 |
| --- | --- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

在客户端更新元素。

**`with_legend`**`(titles: List[str], kwargs: Any)`

向绘图添加图例。

|  参数 | 描述 |
| --- | --- |
| titles: |  线的标题列表 |
| kwargs: |  应传递给 [pyplot.legend](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.legend.html) 的其他参数 |

## Inheritance

- `Pyplot`
- `Element`
- `Visibility`
