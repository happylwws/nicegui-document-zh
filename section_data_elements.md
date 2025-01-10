# *Data* Elements

## Table

基于 Quasar 的 [QTable](https://quasar.dev/vue-components/table) 组件的表格。

| 参数 | 描述 |
| --- | --- |
| rows: | 行对象的列表 |
| columns: | 列对象的列表 (默认为第一行的列) |
| column_defaults: | 可选的默认列属性 |
| row_key: | 包含唯一标识行的列的名称 (默认: "id") |
| title: | 表格的标题 |
| selection: | 选择类型 ("single" 或 "multiple"; 默认: `None`) |
| pagination: | 与分页对象或每页行数相对应的字典 (`None` 隐藏分页，0 表示 "无限"；默认: `None`)。 |
| on_select: | 当选择更改时调用的回调函数 |
| on_pagination_change: | 当分页更改时调用的回调函数 |

如果选择是 'single' 或 'multiple'，则可以使用包含所选行的 `selected` 属性。

```python
from nicegui import ui

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name', 'required': True, 'align': 'left'},
    {'name': 'age', 'label': 'Age', 'field': 'age', 'sortable': True},
]
rows = [
    {'name': 'Alice', 'age': 18},
    {'name': 'Bob', 'age': 21},
    {'name': 'Carol'},
]
ui.table(columns=columns, rows=rows, row_key='name')

ui.run()
```

See [more...](table)

## AG Grid

使用 [AG Grid](https://www.ag-grid.com/) 创建网格的元素。

方法 `run_grid_method` 和 `run_row_method` 可用于与客户端的 AG Grid 实例进行交互。

| 参数 | 描述 |
| --- | --- |
| options: | AG Grid 选项的字典 |
| html_columns: | 应该作为 HTML 渲染的列列表（默认: `[]`） |
| theme: | AG Grid 主题（默认: 'balham'） |
| auto_size_columns: | 是否自动调整列宽以适应网格宽度（默认: `True`） |

```python
from nicegui import ui

grid = ui.aggrid({
    'defaultColDef': {'flex': 1},
    'columnDefs': [
        {'headerName': 'Name', 'field': 'name'},
        {'headerName': 'Age', 'field': 'age'},
        {'headerName': 'Parent', 'field': 'parent', 'hide': True},
    ],
    'rowData': [
        {'name': 'Alice', 'age': 18, 'parent': 'David'},
        {'name': 'Bob', 'age': 21, 'parent': 'Eve'},
        {'name': 'Carol', 'age': 42, 'parent': 'Frank'},
    ],
    'rowSelection': 'multiple',
}).classes('max-h-40')

def update():
    grid.options['rowData'][0]['age'] += 1
    grid.update()

ui.button('Update', on_click=update)
ui.button('Select all', on_click=lambda: grid.run_grid_method('selectAll'))
ui.button('Show parent', on_click=lambda: grid.run_grid_method('setColumnsVisible', ['parent'], True))

ui.run()
```

See [more...](aggrid)

## Highcharts chart

使用 [Highcharts](https://www.highcharts.com/) 创建图表的元素。
可以通过更改 `options` 属性将更新推送到图表。
数据更改后，调用 `update` 方法刷新图表。

由于 Highcharts 的限制性许可，此元素不属于标准的 NiceGUI 包。
它维护在 [单独的存储库](https://github.com/zauberzeug/nicegui-highcharts/) 中，可以使用 `pip install nicegui[highcharts]` 安装。

默认情况下，会创建一个 `Highcharts.chart`。
要使用例如 `Highcharts.stockChart`，请将 `type` 属性设置为 "stockChart"。

| 参数 | 描述 |
| --- | --- |
| options: | Highcharts 选项的字典 |
| type: | 图表类型（例如 "chart"、"stockChart"、"mapChart" 等；默认: "chart"） |
| extras: | 要包含的额外依赖项列表（例如 "annotations"、"arc-diagram"、"solid-gauge" 等） |
| on_point_click: | 点击某个点时调用的回调函数 |
| on_point_drag_start: | 当开始拖动一个点时调用的回调函数 |
| on_point_drag: | 当拖动一个点时调用的回调函数 |
| on_point_drop: | 当一个点被放下时调用的回调函数 |

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

See [more...](highchart)

## Apache EChart

使用 [ECharts](https://echarts.apache.org/) 创建图表的元素。
可以通过更改 `options` 属性将更新推送到图表。
数据更改后，调用 `update` 方法刷新图表。

| 参数 | 描述 |
| --- | --- |
| options: | EChart 选项的字典 |
| on_click_point: | 当点击一个点时调用的回调函数 |
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

See [more...](echart)

## Pyplot Context

创建一个用于配置 [Matplotlib](https://matplotlib.org/) 图的上下文。

| 参数 | 描述 |
| --- | --- |
| close: |  退出上下文后是否应关闭图形；如果要稍后更新它，请设置为 `False` (默认: `True`) |
| kwargs: | 像 `figsize` 这样的参数，应该传递给 [pyplot.figure](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.figure.html) |

```python
import numpy as np
from matplotlib import pyplot as plt
from nicegui import ui

with ui.pyplot(figsize=(3, 2)):
    x = np.linspace(0.0, 5.0)
    y = np.cos(2 * np.pi * x) * np.exp(-x)
    plt.plot(x, y, '-')

ui.run()
```

See [more...](pyplot)

## Matplotlib

创建一个 [Matplotlib](https://matplotlib.org/) 元素，渲染 Matplotlib 图形。
当离开图形上下文时，该图形会自动更新。

| 参数 | 描述 |
| --- | --- |
| kwargs: | 像 `figsize` 这样的参数，应该传递给 [matplotlib.figure.Figure](https://matplotlib.org/stable/api/figure_api.html#matplotlib.figure.Figure) |

```python
import numpy as np
from nicegui import ui

with ui.matplotlib(figsize=(3, 2)).figure as fig:
    x = np.linspace(0.0, 5.0)
    y = np.cos(2 * np.pi * x) * np.exp(-x)
    ax = fig.gca()
    ax.plot(x, y, '-')

ui.run()
```

See [more...](matplotlib)

## Line Plot

使用 pyplot 创建折线图。
当与 `ui.timer` 结合使用时，`push` 方法提供实时更新。

| 参数 | 描述 |
| --- | --- |
| n: |  线条的数量 |
| limit: | 每条线的数据点的最大数量 (新点将取代最旧的点) |
| update_every: | 仅在多次推送新数据后才更新绘图，以节省 CPU 和带宽 |
| close: |  退出上下文后是否应关闭图形；如果要稍后更新它，请设置为 `False` (默认: `True`) |
| kwargs: | 像 `figsize` 这样的参数，应该传递给 [pyplot.figure](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.figure.html) |

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

See [more...](line_plot)

## Plotly Element

渲染 Plotly 图表。
有两种方法可以传递 Plotly 图进行渲染，请参阅参数 `figure`：

*   传递一个 `go.Figure` 对象，请参阅 [https://plotly.com/python/](https://plotly.com/python/)
*   传递一个带有键 `data`、`layout`、`config` (可选) 的 Python `dict` 对象，请参阅 [https://plotly.com/javascript/](https://plotly.com/javascript/)

为了获得最佳性能，请使用声明式 `dict` 方法来创建 Plotly 图表。

| 参数 | 描述 |
| --- | --- |
| figure: | 要渲染的 Plotly 图。可以是 `go.Figure` 实例，也可以是具有键 `data`、`layout`、`config` (可选) 的 `dict` 对象。 |

```python
import plotly.graph_objects as go
from nicegui import ui

fig = go.Figure(go.Scatter(x=[1, 2, 3, 4], y=[1, 2, 3, 2.5]))
fig.update_layout(margin=dict(l=0, r=0, t=0, b=0))
ui.plotly(fig).classes('w-full h-40')

ui.run()
```

See [more...](plotly)

## Linear Progress

一个线性进度条，包装了 Quasar 的 [QLinearProgress](https://quasar.dev/vue-components/linear-progress) 组件。

| 参数 | 描述 |
| --- | --- |
| value: |  字段的初始值 (从 0.0 到 1.0) |
| size: | 进度条的高度 (默认：带值标签为 "20px"，不带值标签为 "4px") |
| show_value: | 是否在中心显示值标签 (默认: `True`) |
| color: | 颜色 (Quasar、Tailwind 或 CSS 颜色或 `None`，默认: "primary") |

```python
from nicegui import ui

slider = ui.slider(min=0, max=1, step=0.01, value=0.5)
ui.linear_progress().bind_value_from(slider, 'value')

ui.run()
```

See [more...](linear_progress)

## Circular Progress

一个圆形进度条，包装了 Quasar 的 [QCircularProgress](https://quasar.dev/vue-components/circular-progress)。

| 参数 | 描述 |
| --- | --- |
| value: | 字段的初始值 |
| min: | 最小值 (默认: 0.0) |
| max: | 最大值 (默认: 1.0) |
| size: | 进度圆圈的大小 (默认: "xl") |
| show_value: | 是否在中心显示值标签 (默认: `True`) |
| color: | 颜色 (Quasar、Tailwind 或 CSS 颜色或 `None`，默认: "primary") |

```python
from nicegui import ui

slider = ui.slider(min=0, max=1, step=0.01, value=0.5)
ui.circular_progress().bind_value_from(slider, 'value')

ui.run()
```

See [more...](circular_progress)

## Spinner

此元素基于 Quasar 的 [QSpinner](https://quasar.dev/vue-components/spinners) 组件。

| 参数 | 描述 |
| --- | --- |
| type: | spinner 的类型 (例如 "audio"、"ball"、"bars"、...，默认: "default") |
| size: | spinner 的大小 (例如 "3em"、"10px"、"xl"、...，默认: "1em") |
| color: | spinner 的颜色 (Quasar、Tailwind 或 CSS 颜色或 `None`，默认: "primary") |
| thickness: | spinner 的厚度 (仅适用于 "default" spinner，默认: 5.0) |

```python
from nicegui import ui

with ui.row():
    ui.spinner(size='lg')
    ui.spinner('audio', size='lg', color='green')
    ui.spinner('dots', size='lg', color='red')

ui.run()
```

See [more...](spinner)

## 3D Scene

使用 [three.js](https://threejs.org/) 显示 3D 场景。
目前，NiceGUI 支持盒子、球体、圆柱体/圆锥体、挤压体、直线、曲线和纹理网格。
对象可以平移、旋转并以不同的颜色、不透明度或线框显示。
它们也可以分组以应用联合运动。

| 参数 | 描述 |
| --- | --- |
| width: | 画布的宽度 |
| height: | 画布的高度 |
| grid: | 是否显示网格 (布尔值或用于 [Three.js 的 GridHelper](https://threejs.org/docs/#api/en/helpers/GridHelper) 的 `size` 和 `divisions` 的元组，默认值: 100x100) |
| camera: | 相机定义，可以是 `ui.scene.perspective_camera` (默认) 或 `ui.scene.orthographic_camera` 的实例 |
| on_click: | 单击 3D 对象时执行的回调函数 (使用 `click_events` 指定要订阅的事件) |
| click_events: | 要订阅的 JavaScript 点击事件列表 (默认: `['click', 'dblclick']`) |
| on_drag_start: |  当开始拖动 3D 对象时执行的回调函数 |
| on_drag_end: |  当 3D 对象被放下时执行的回调函数 |
| drag_constraints: | 用逗号分隔的 JavaScript 表达式，用于约束拖动对象的位置 (例如，`'x = 0, z = y / 2'`) |
| background_color: | 场景的背景颜色 (默认: "#eee") |

```python
from nicegui import ui

with ui.scene().classes('w-full h-64') as scene:
    scene.axes_helper()
    scene.sphere().material('#4488ff').move(2, 2)
    scene.cylinder(1, 0.5, 2, 20).material('#ff8800', opacity=0.5).move(-2, 1)
    scene.extrusion([[0, 0], [0, 1], [1, 0.5]], 0.1).material('#ff8888').move(2, -1)

    with scene.group().move(z=2):
        scene.box().move(x=2)
        scene.box().move(y=2).rotate(0.25, 0.5, 0.75)
        scene.box(wireframe=True).material('#888888').move(x=2, y=2)

    scene.line([-4, 0, 0], [-4, 2, 0]).material('#ff0000')
    scene.curve([-4, 0, 0], [-4, -1, 0], [-3, -1, 0], [-3, 0, 0]).material('#008800')

    logo = 'https://avatars.githubusercontent.com/u/2843826'
    scene.texture(logo, [[[0.5, 2, 0], [2.5, 2, 0]],
                         [[0.5, 0, 0], [2.5, 0, 0]]]).move(1, -3)

    teapot = 'https://upload.wikimedia.org/wikipedia/commons/9/93/Utah_teapot_(solid).stl'
    scene.stl(teapot).scale(0.2).move(-3, 4)

    avocado = 'https://raw.githubusercontent.com/KhronosGroup/glTF-Sample-Assets/main/Models/Avocado/glTF-Binary/Avocado.glb'
    scene.gltf(avocado).scale(40).move(-2, -3, 0.5)

    scene.text('2D', 'background: rgba(0, 0, 0, 0.2); border-radius: 5px; padding: 5px').move(z=2)
    scene.text3d('3D', 'background: rgba(0, 0, 0, 0.2); border-radius: 5px; padding: 5px').move(y=-2).scale(.05)

ui.run()
```

See [more...](scene)

## Leaflet map

此元素是 [Leaflet](https://leafletjs.com/) JavaScript 库的包装器。

| 参数 | 描述 |
| --- | --- |
| center: | 地图的初始中心位置（纬度/经度，默认值：(0.0, 0.0)） |
| zoom: | 地图的初始缩放级别（默认值：13） |
| draw_control: | 是否显示绘图工具栏（默认值：False） |
| options: | 传递给 Leaflet 地图的其他选项（默认值：{}） |
| hide_drawn_items: | 是否在地图上隐藏绘制的项目（默认值：False） |

```python
from nicegui import ui

m = ui.leaflet(center=(51.505, -0.09))
ui.label().bind_text_from(m, 'center', lambda center: f'Center: {center[0]:.3f}, {center[1]:.3f}')
ui.label().bind_text_from(m, 'zoom', lambda zoom: f'Zoom: {zoom}')

with ui.grid(columns=2):
    ui.button('London', on_click=lambda: m.set_center((51.505, -0.090)))
    ui.button('Berlin', on_click=lambda: m.set_center((52.520, 13.405)))
    ui.button(icon='zoom_in', on_click=lambda: m.set_zoom(m.zoom + 1))
    ui.button(icon='zoom_out', on_click=lambda: m.set_zoom(m.zoom - 1))

ui.run()
```

See [more...](leaflet)

## Tree

使用 Quasar 的 [QTree](https://quasar.dev/vue-components/tree) 组件显示分层数据。

如果使用 ID，请确保它们在整个树中是唯一的。

要使用复选框和 `on_tick`，请将 `tick_strategy` 参数设置为 "leaf"、"leaf-filtered" 或 "strict"。

| 参数 | 描述 |
| --- | --- |
| nodes: | 节点对象的分层列表 |
| node_key: | 每个节点对象中保存其唯一 id 的属性名称（默认值：“id”） |
| label_key: | 每个节点对象中保存其标签的属性名称（默认值：“label”） |
| children_key: | 每个节点对象中保存其子列表的属性名称（默认值：“children”） |
| on_select: | 当节点选择更改时调用的回调函数 |
| on_expand: | 当节点展开状态更改时调用的回调函数 |
| on_tick: | 当节点被选中或取消选中时调用的回调函数 |
| tick_strategy: | 是否以及如何使用复选框 ("leaf", "leaf-filtered" 或 "strict"；默认: `None`) |

```python
from nicegui import ui

ui.tree([
    {'id': 'numbers', 'children': [{'id': '1'}, {'id': '2'}]},
    {'id': 'letters', 'children': [{'id': 'A'}, {'id': 'B'}]},
], label_key='id', on_select=lambda e: ui.notify(e.value))

ui.run()
```

See [more...](tree)

## Log View

创建一个日志视图，允许添加新行而无需将整个历史记录重新传输到客户端。

| 参数 | 描述 |
| --- | --- |
| max_lines: | 在删除最旧的行之前的最大行数 (默认值: `None`) |

```python
from datetime import datetime
from nicegui import ui

log = ui.log(max_lines=10).classes('w-full h-20')
ui.button('Log time', on_click=lambda: log.push(datetime.now().strftime('%X.%f')[:-5]))

ui.run()
```

See [more...](log)

## Editor

基于 [Quasar 的 QEditor](https://quasar.dev/vue-components/editor) 的所见即所得编辑器。
该值是一个字符串，其中包含格式化文本作为 HTML 代码。

| 参数 | 描述 |
| --- | --- |
| value: | 初始值 |
| on_change: | 当值更改时要调用的回调函数 |

```python
from nicegui import ui

editor = ui.editor(placeholder='Type something here')
ui.markdown().bind_content_from(editor, 'value',
                                backward=lambda v: f'HTML code:\n```\n{v}\n```')

ui.run()
```

See [more...](editor)

## Code

此元素显示带有语法高亮的代码块。

在安全环境（HTTPS 或 localhost）中，会显示一个复制按钮，用于将代码复制到剪贴板。

| 参数 | 描述 |
| --- | --- |
| content: | 要显示的代码 |
| language: | 代码的语言 (默认: "python") |

```python
from nicegui import ui

ui.code('''
    from nicegui import ui

    ui.label('Code inception!')

    ui.run()
''').classes('w-full')

ui.run()
```

See [more...](code)

## JSONEditor

使用 [JSONEditor](https://github.com/josdejong/svelte-jsoneditor) 创建 JSON 编辑器的元素。
可以通过更改 `properties` 属性将更新推送到编辑器。
数据更改后，调用 `update` 方法刷新编辑器。

| 参数 | 描述 |
| --- | --- |
| properties: | JSONEditor 属性的字典 |
| on_select: | 当某些内容被选中时调用的回调函数 |
| on_change: | 当内容更改时调用的回调函数 |
| schema: | 用于验证正在编辑的数据的可选 [JSON schema](https://json-schema.org/) |

```python
from nicegui import ui

json = {
    'array': [1, 2, 3],
    'boolean': True,
    'color': '#82b92c',
    None: None,
    'number': 123,
    'object': {
        'a': 'b',
        'c': 'd',
    },
    'time': 1575599819000,
    'string': 'Hello World',
}
ui.json_editor({'content': {'json': json}},
               on_select=lambda e: ui.notify(f'Select: {e}'),
               on_change=lambda e: ui.notify(f'Change: {e}'))

ui.run()
```

See [more...](json_editor)
