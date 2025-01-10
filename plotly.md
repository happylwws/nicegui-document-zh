# ui.plotly

## Plotly Element

渲染 Plotly 图表。
有两种方法可以传递用于渲染的 Plotly 图形，请参见参数 `figure`：

*   传递一个 `go.Figure` 对象，参见 [https://plotly.com/python/](https://plotly.com/python/)
*   传递一个 Python `dict` 对象，其中包含键 `data`、`layout`、`config`（可选），参见 [https://plotly.com/javascript/](https://plotly.com/javascript/)

为了获得最佳性能，请使用声明式 `dict` 方法创建 Plotly 图表。

|  参数 | 描述 |
| --- | --- |
| figure: | 要渲染的 Plotly 图形。可以是 `go.Figure` 实例，也可以是包含键 `data`、`layout`、`config`（可选）的 `dict` 对象。 |

```python
import plotly.graph_objects as go
from nicegui import ui

fig = go.Figure(go.Scatter(x=[1, 2, 3, 4], y=[1, 2, 3, 2.5]))
fig.update_layout(margin=dict(l=0, r=0, t=0, b=0))
ui.plotly(fig).classes('w-full h-40')

ui.run()
```

## Dictionary interface

此演示展示了如何使用声明式字典接口创建绘图。
对于具有许多轨迹和数据点的绘图，这比面向对象的接口更有效。
该定义对应于 [JavaScript Plotly API](https://plotly.com/javascript/)。
由于默认值不同，生成的绘图可能与使用面向对象接口创建的同一绘图略有不同，但功能相同。

```python
from nicegui import ui

fig = {
    'data': [
        {
            'type': 'scatter',
            'name': 'Trace 1',
            'x': [1, 2, 3, 4],
            'y': [1, 2, 3, 2.5],
        },
        {
            'type': 'scatter',
            'name': 'Trace 2',
            'x': [1, 2, 3, 4],
            'y': [1.4, 1.8, 3.8, 3.2],
            'line': {'dash': 'dot', 'width': 3},
        },
    ],
    'layout': {
        'margin': {'l': 15, 'r': 0, 't': 0, 'b': 15},
        'plot_bgcolor': '#E5ECF6',
        'xaxis': {'gridcolor': 'white'},
        'yaxis': {'gridcolor': 'white'},
    },
}
ui.plotly(fig).classes('w-full h-40')

ui.run()
```

## Plot updates

此演示展示了如何实时更新绘图。
单击按钮以向绘图添加新轨迹。
要将新绘图发送到浏览器，请确保显式调用 `plot.update()` 或 `ui.update(plot)`。

```python
import plotly.graph_objects as go
from nicegui import ui
from random import random

fig = go.Figure()
fig.update_layout(margin=dict(l=0, r=0, t=0, b=0))
plot = ui.plotly(fig).classes('w-full h-40')

def add_trace():
    fig.add_trace(go.Scatter(x=[1, 2, 3], y=[random(), random(), random()]))
    plot.update()

ui.button('Add trace', on_click=add_trace)

ui.run()
```

## Plot events

此演示展示了如何处理 Plotly 事件。
尝试单击一个数据点以查看事件数据。

目前，支持以下事件：
"plotly_click",
"plotly_legendclick",
"plotly_selecting",
"plotly_selected",
"plotly_hover",
"plotly_unhover",
"plotly_legenddoubleclick",
"plotly_restyle",
"plotly_relayout",
"plotly_webglcontextlost",
"plotly_afterplot",
"plotly_autosize",
"plotly_deselect",
"plotly_doubleclick",
"plotly_redraw",
"plotly_animated".
有关更多信息，请参见 [Plotly 文档](https://plotly.com/javascript/plotlyjs-events/)。

```python
import plotly.graph_objects as go
from nicegui import ui

fig = go.Figure(go.Scatter(x=[1, 2, 3, 4], y=[1, 2, 3, 2.5]))
fig.update_layout(margin=dict(l=0, r=0, t=0, b=0))
plot = ui.plotly(fig).classes('w-full h-40')
plot.on('plotly_click', ui.notify)

ui.run()
```

## Reference

### Initializer

|  参数 | 描述 |
| --- | --- |
| figure: | 要渲染的 Plotly 图形。可以是 `go.Figure` 实例，也可以是 |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`is_deleted`**`: 'bool'`

元素是否已被删除。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`props`**`: 'Props[Self]'`

元素的属性。

**`style`**`: 'Style[Self]'`

元素的样式。

**`visible`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

向元素添加资源。

|  参数 | 描述 |
| --- | --- |
| path: | 资源路径（例如，包含 CSS 和 JavaScript 文件的文件夹） |

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

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值发生更改时发生。
后向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 应用于值，然后再将其应用于目标的函数。 |
| backward: | 应用于值，然后再将其应用于此元素的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从目标对象的 target_name 属性绑定此元素的可见性。

绑定仅以一种方式工作，从目标到此元素。
更新会立即发生，并在值发生更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 应用于值，然后再将其应用于此元素的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从该元素到目标。
更新会立即发生，并在值发生更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 应用于值，然后再将其应用于目标的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素都将共享这些 HTML 类。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的类字符串 |
| remove: | 要从元素中删除的以空格分隔的类字符串 |
| toggle: | 要切换的以空格分隔的类字符串 |
| replace: | 要使用的以空格分隔的类字符串，而不是现有的类 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或其布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素都将共享这些属性。
这些必须在元素实例化之前定义。

如果未指定值，则布尔属性将假定为 `True`。

|  参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的布尔值列表或要添加的键=值对 |
| remove: | 要删除的属性键的以空格分隔的列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。
此类的所有元素都将共享这些 CSS 定义。
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

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

应等待此函数，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最长时间（默认值：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于标识使用 [ElementFilter](/documentation/element_filter) 查询的元素，
该标记在测试中大量使用，
但也可以用于减少全局变量的数量或传递依赖项。

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
| handler: | 在事件发生时调用的回调 |
| args: | 事件消息中包含的参数，该事件消息已发送给事件处理程序（默认值：`None`，表示全部） |
| throttle: | 事件发生之间的最短时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 在事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`remove`**`(element: Union[Element, int]) -> None`

删除子元素。

|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果等待该函数，则会返回方法调用的结果。
否则，将执行该方法，而无需等待响应。

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 要传递给方法的参数 |
| timeout: | 等待响应的最长时间（默认值：1 秒） |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

|  参数 | 描述 |
| --- | --- |
| visible: | 元素是否应可见。 |

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

|  参数 | 描述 |
| --- | --- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

**`update_figure`**`(figure: Union[Dict, go.Figure])`

覆盖此 Plotly 图表的图形实例并在客户端更新图表。

### Inheritance

*   `Element`
*   `Visibility`
