# ui. *echart*

## Apache EChart

这是一个使用 [ECharts](https://echarts.apache.org/) 创建图表的元素。
可以通过更改 `options` 属性将更新推送到图表。
数据更改后，调用 `update` 方法刷新图表。

| 选项 | 描述 |
|---|---|
| options: | EChart 选项的字典 |
| on_click_point: | 单击点时调用的回调 |
| enable_3d: | 强制导入 echarts-gl 库 |
| renderer: | 要使用的渲染器 ("canvas" 或 "svg") |

```python
from nicegui import ui
from random import random

echart = ui.echart({
    'xAxis': {'type': 'value'},
    'yAxis': {'type': 'category', 'data': ['A', 'B'], 'inverse': True},
    'legend': {'textStyle': {'color': 'gray'}},
    'series': [
        {'type': 'bar', 'name': 'Alpha', 'data': [0.1, 0.2]},
        {'type': 'bar', 'name': 'Beta', 'data': [0.3, 0.4]},
    ],
})

def update():
    echart.options['series'][0]['data'][0] = random()
    echart.update()

ui.button('Update', on_click=update)

ui.run()
```

## EChart with clickable points

您可以为单击系列点时的事件注册回调。

```python
from nicegui import ui

ui.echart({
    'xAxis': {'type': 'category'},
    'yAxis': {'type': 'value'},
    'series': [{'type': 'line', 'data': [20, 10, 30, 50, 40, 30]}],
}, on_point_click=ui.notify)

ui.run()
```

## EChart with dynamic properties

可以将动态属性传递给图表元素以进行自定义，例如应用轴标签格式。
要使属性动态化，请在属性名称前加上冒号“:”。

```python
from nicegui import ui

ui.echart({
    'xAxis': {'type': 'category'},
    'yAxis': {'axisLabel': {':formatter': 'value => "$" + value'}},
    'series': [{'type': 'line', 'data': [5, 8, 13, 21, 34, 55]}],
})

ui.run()
```

## EChart from pyecharts

您可以使用 `from_pyecharts` 方法从 pyecharts 对象创建 EChart 元素。
对于定义像格式化函数这样的动态选项，您可以使用 `pyecharts.commons.utils` 中的 `JsCode` 类。
或者，您可以使用冒号“:”作为属性名称的前缀，以表明该值是 JavaScript 表达式。

```python
from nicegui import ui
from pyecharts.charts import Bar
from pyecharts.commons.utils import JsCode
from pyecharts.options import AxisOpts

ui.echart.from_pyecharts(
    Bar()
    .add_xaxis(['A', 'B', 'C'])
    .add_yaxis('ratio', [1, 2, 4])
    .set_global_opts(
        xaxis_opts=AxisOpts(axislabel_opts={
            ':formatter': r'(val, idx) => `group ${val}`',
        }),
        yaxis_opts=AxisOpts(axislabel_opts={
            'formatter': JsCode(r'(val, idx) => `${val}%`'),
        }),
    )
)

ui.run()
```

## Run methods

您可以使用 `run_chart_method` 方法运行 EChart 实例的方法。
此演示展示了如何显示和隐藏加载动画，如何获取图表的当前宽度，以及如何使用自定义格式化程序添加工具提示。

方法名称 "setOption" 前面的冒号 ":" 表示该参数是一个 JavaScript 表达式，在传递给方法之前在客户端上进行评估。

请注意，仅页面函数支持从客户端请求数据，共享自动索引页面不支持。

```python
from nicegui import ui

@ui.page('/')
def page():
    echart = ui.echart({
        'xAxis': {'type': 'category', 'data': ['Mon', 'Tue', 'Wed', 'Thu', 'Fri']},
        'yAxis': {'type': 'value'},
        'series': [{'type': 'line', 'data': [150, 230, 224, 218, 135]}],
    })

    ui.button('Show Loading', on_click=lambda: echart.run_chart_method('showLoading'))
    ui.button('Hide Loading', on_click=lambda: echart.run_chart_method('hideLoading'))

    async def get_width():
        width = await echart.run_chart_method('getWidth')
        ui.notify(f'Width: {width}')
    ui.button('Get Width', on_click=get_width)

    ui.button('Set Tooltip', on_click=lambda: echart.run_chart_method(
        ':setOption', r'{tooltip: {formatter: params => "$" + params.value}}',
    ))

ui.run()
```

## Arbitrary chart events

您可以使用 `on` 方法和 "chart:" 前缀为图表注册任意事件侦听器。
此演示展示了如何为 "selectchanged" 事件注册回调，该事件在用户选择一个点时触发。

```python
from nicegui import ui

ui.echart({
    'toolbox': {'feature': {'brush': {'type': ['rect']}}},
    'brush': {},
    'xAxis': {'type': 'category'},
    'yAxis': {'type': 'value'},
    'series': [{'type': 'line', 'data': [1, 2, 3]}],
}).on('chart:selectchanged', lambda e: label.set_text(
    f'Selected point {e.args["fromActionPayload"]["dataIndexInside"]}'
))
label = ui.label()

ui.run()
```

## 3D Graphing

如果初始选项包含字符串“3D”，则图表将自动启用 3D。
如果不是，请将 `enable_3d` 参数设置为 `True`。

```python
from nicegui import ui

ui.echart({
    'xAxis3D': {},
    'yAxis3D': {},
    'zAxis3D': {},
    'grid3D': {},
    'series': [{
        'type': 'line3D',
        'data': [[1, 1, 1], [3, 3, 3]],
    }],
})

ui.run()
```

## Reference

### Initializer

|  选项 | 描述 |
| --- | --- |
| options: | EChart 选项的字典 |
| on_click_point: | 单击点时调用的回调 |
| enable_3d: | 强制导入 echarts-gl 库 |
| renderer: | 要使用的渲染器 ("canvas" 或 "svg") |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`is_deleted`**`: 'bool'`

元素是否已被删除。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`options`**`: Dict`

选项字典。

**`props`**`: 'Props[Self]'`

元素的属性。

**`style`**`: 'Style[Self]'`

元素的样式。

**`visible`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

将资源添加到元素。

|  参数 | 描述 |
| --- | --- |
| path: | 资源路径（例如，包含 CSS 和 JavaScript 文件的文件夹） |

**`add_slot`**`(name: str, template: Optional[str] = None) -> Slot`

向元素添加一个slot。

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

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值发生更改时进行。
对于初始同步，向后绑定优先。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的功能。 |
| backward: | 在将其应用于此元素之前应用于该值的功能。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此目标对象的 target_name 属性绑定此元素的可见性。

绑定仅以一种方式工作，从目标到此元素。
更新会立即发生，并在值发生更改时进行。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的功能。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从该元素到目标。
更新会立即发生，并在值发生更改时进行。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的功能。 |

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
| add: | 空格分隔的类字符串 |
| remove: | 要从元素中删除的空格分隔的类字符串 |
| toggle: | 要切换的空格分隔的类字符串 |
| replace: | 要使用的空格分隔的类字符串，而不是现有的类 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些属性。
这些必须在元素实例化之前定义。

如果未指定值，则假定布尔属性为 `True`。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加的布尔值或键=值对的空格分隔列表 |
| remove: | 要删除的属性键的空格分隔列表 |

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
| include_self: | 是否在迭代中包括元素本身 |

`@classmethod`
**`from_pyecharts`**`(chart: Chart, on_point_click: Optional[Callable] = None) -> Self`

从 pyecharts 对象创建一个 echart 元素。

|  参数 | 描述 |
| --- | --- |
| chart: | pyecharts 图表对象 |
| on_click_point: | 单击点时调用的回调 |
| return: | echart 元素 |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

此函数应被等待，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于使用 [ElementFilter](/documentation/element_filter) 识别元素进行查询，该查询在测试中大量使用，但也可用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或带有空格分隔的标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认值：父容器） |
| target_index: | 目标 slot 中的索引（默认值：追加到末尾） |
| target_slot: | 目标容器中的 slot（默认值：默认 slot） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅一个事件。

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如，“click”、“mousedown”或“update:model-value”） |
| handler: | 事件发生时调用的回调 |
| args: | 事件消息中包含的发送到事件处理程序的参数（默认值：`None`，表示全部） |
| throttle: | 事件发生之间的最小时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`on_point_click`**`(callback: Union[Callable[[nicegui.events.EChartPointClickEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个在单击点时调用的回调。

**`remove`**`(element: Union[Element, int]) -> None`

删除一个子元素。

|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`run_chart_method`**`(name: str, *args, timeout: float = 1) -> nicegui.awaitable_response.AwaitableResponse`

运行 EChart 实例的方法。

有关方法列表，请参阅 [ECharts 文档](https://echarts.apache.org/en/api.html#echartsInstance)。

如果该函数被等待，则返回方法调用的结果。
否则，将执行该方法而不等待响应。

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称（前缀“:”表示参数是 JavaScript 表达式） |
| args: | 传递给方法的参数（Python 对象或 JavaScript 表达式） |
| timeout: | 超时时间（以秒为单位）（默认值：1 秒） |
| return: | 可以等待以获取方法调用结果的 AwaitableResponse |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果该函数被等待，则返回方法调用的结果。
否则，将执行该方法而不等待响应。

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 传递给方法的参数 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

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

### Inheritance

*   `Element`
*   `Visibility`
