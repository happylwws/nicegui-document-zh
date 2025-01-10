# ui.highchart

## Highcharts chart

这是一个使用 [Highcharts](https://www.highcharts.com/) 创建图表的元素。
可以通过更改 `options` 属性将更新推送到图表。
数据更改后，调用 `update` 方法刷新图表。

由于 Highcharts 的许可限制，此元素不属于标准的 NiceGUI 包。
它维护在 [单独的存储库](https://github.com/zauberzeug/nicegui-highcharts/) 中，可以使用 `pip install nicegui[highcharts]` 安装。

默认情况下，会创建一个 `Highcharts.chart`。
要使用例如 `Highcharts.stockChart`，请将 `type` 属性设置为 "stockChart"。

| 选项 | 描述 |
| --- | --- |
| options: | Highcharts 选项的字典 |
| type: | 图表类型（例如 "chart", "stockChart", "mapChart", ...；默认值: "chart"） |
| extras: | 要包含的额外依赖项列表（例如 "annotations", "arc-diagram", "solid-gauge", ...） |
| on_point_click: | 单击点时调用的回调函数 |
| on_point_drag_start: | 当点开始拖动时调用的回调函数 |
| on_point_drag: | 当点被拖动时调用的回调函数 |
| on_point_drop: | 当点被放下时调用的回调函数 |

```python
from nicegui import ui
from random import random

chart = ui.highchart({
    'title': False,
    'chart': {'type': 'bar'},
    'xAxis': {'categories': ['A', 'B']},
    'series': [
        {'name': 'Alpha', 'data': [0.1, 0.2]},
        {'name': 'Beta', 'data': [0.3, 0.4]},
    ],
}).classes('w-full h-64')

def update():
    chart.options['series'][0]['data'][0] = random()
    chart.update()

ui.button('Update', on_click=update)

ui.run()
```

## Chart with extra dependencies

要使用默认依赖项中未包含的图表类型，可以指定额外的依赖项。
此演示展示了一个实心仪表图。

```python
from nicegui import ui

ui.highchart({
    'title': False,
    'chart': {'type': 'solidgauge'},
    'yAxis': {
        'min': 0,
        'max': 1,
    },
    'series': [
        {'data': [0.42]},
    ],
}, extras=['solid-gauge']).classes('w-full h-64')

ui.run()
```

## Chart with draggable points

此图表允许拖动序列点。
您可以为以下事件注册回调：

- `on_point_click`: 当一个点被点击时调用
- `on_point_drag_start`: 当一个点开始拖动时调用
- `on_point_drag`: 当一个点被拖动时调用
- `on_point_drop`: 当一个点被放下时调用

```python
from nicegui import ui

ui.highchart(
    {
        'title': False,
        'plotOptions': {
            'series': {
                'stickyTracking': False,
                'dragDrop': {'draggableY': True, 'dragPrecisionY': 1},
            },
        },
        'series': [
            {'name': 'A', 'data': [[20, 10], [30, 20], [40, 30]]},
            {'name': 'B', 'data': [[50, 40], [60, 50], [70, 60]]},
        ],
    },
    extras=['draggable-points'],
    on_point_click=lambda e: ui.notify(f'Click: {e}'),
    on_point_drag_start=lambda e: ui.notify(f'Drag start: {e}'),
    on_point_drop=lambda e: ui.notify(f'Drop: {e}')
).classes('w-full h-64')

ui.run()
```

## Reference

### Initializer

|  参数 | 描述 |
| --- | --- |
| options: | Highcharts 选项的字典 |
| type: | 图表类型 (例如: "chart", "stockChart", "mapChart", ...; 默认值: "chart") |
| extras: | 要包含的额外依赖项列表 (例如: "annotations", "arc-diagram", "solid-gauge", ...) |
| on_point_click: | 单击点时调用的回调函数 |
| on_point_drag_start: | 当点开始拖动时调用的回调函数 |
| on_point_drag: | 当点被拖动时调用的回调函数 |
| on_point_drop: | 当点被放下时调用的回调函数 |

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
| include_self: | 是否在迭代中包括元素本身 |
 
**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`
 
将此元素的可见性绑定到目标对象的 target_name 属性。
 
绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
反向绑定优先用于初始同步。
 
|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于值的函数。 |
| backward: | 在将值应用于此元素之前应用于值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |
 
**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`
 
从此目标对象的 target_name 属性绑定此元素的可见性。
 
绑定仅以一种方式工作，从目标到此元素。
更新会立即发生，并在值更改时发生。
 
|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |
 
**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`
 
将此元素的可见性绑定到目标对象的 target_name 属性。
 
绑定仅以一种方式工作，从该元素到目标。
更新会立即发生，并在值更改时发生。
 
|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于值的函数。 |
 
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
| replace: | 要使用的以空格分隔的类字符串，以代替现有的类 |
 
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
| replace: | 要使用的以分号分隔的样式列表，以代替现有的样式 |
 
**`delete`**`() -> None`
 
删除该元素及其所有子元素。
 
**`descendants`**`(include_self: bool = False) -> Iterator[Element]`
 
迭代元素的后代。
 
|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |
 
**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`
 
返回计算属性。
 
应该等待此函数，以便正确返回计算的属性。
 
|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最大时间 (默认值: 1 秒) |
 
**`mark`**`(*markers: str) -> Self`
 
替换元素的标记。
 
标记用于使用 [ElementFilter](/documentation/element_filter) 查询元素，
该过滤器在测试中大量使用，
但也可用于减少全局变量的数量或传递依赖项。
 
|  参数 | 描述 |
| --- | --- |
| markers: | 带有空格分隔标记的字符串列表或单个字符串；替换现有标记 |
 
**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`
 
将元素移动到另一个容器。
 
|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器 (默认值: 父容器) |
| target_index: | 目标 slot 中的索引 (默认值: 追加到末尾) |
| target_slot: | 目标容器中的 slot (默认值: 默认 slot) |
 
**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`
 
订阅事件。
 
|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称 (例如: "click", "mousedown" 或 "update:model-value") |
| handler: | 在事件发生时调用的回调函数 |
| args: | 包含在发送给事件处理程序的事件消息中的参数 (默认值: `None` 表示全部) |
| throttle: | 事件发生之间的最短时间 (以秒为单位) (默认值: 0.0) |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序 (默认值: `True`) |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序 (默认值: `True`) |
| js_handler: | 在事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)` (默认值: `None`) |
 
**`remove`**`(element: Union[Element, int]) -> None`
 
删除子元素。
 
|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |
 
**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`
 
在客户端运行方法。
 
如果该函数被等待，则返回方法调用的结果。
否则，该方法将在不等待响应的情况下执行。
 
|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 传递给方法的参数 |
| timeout: | 等待响应的最大时间 (默认值: 1 秒) |
 
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

*   `Visibility`
