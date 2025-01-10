# ui.leaflet

## Leaflet map

这个元素是 [Leaflet](https://leafletjs.com/) JavaScript 库的包装器。

| 参数             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| center:          | 地图的初始中心位置（纬度/经度，默认为 (0.0, 0.0)）             |
| zoom:            | 地图的初始缩放级别（默认为 13）                               |
| draw_control:    | 是否显示绘制工具栏（默认为 False）                            |
| options:         | 传递给 Leaflet 地图的其他选项（默认为 {}）                    |
| hide_drawn_items: | 是否隐藏地图上绘制的项目（默认为 False）                     |

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

## Changing the Map Style

默认地图样式是 OpenStreetMap。
您可以在 [https://leaflet-extras.github.io/leaflet-providers/preview/](https://leaflet-extras.github.io/leaflet-providers/preview/) 找到更多地图样式。
每次调用 `tile_layer` 都会在前一次的基础上堆叠。
因此，如果您想更改地图样式，您必须先删除默认样式。

```python
from nicegui import ui

m = ui.leaflet(center=(51.505, -0.090), zoom=3)
m.clear_layers()
m.tile_layer(
    url_template=r'https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png',
    options={
        'maxZoom': 17,
        'attribution':
            'Map data: &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, <a href="https://viewfinderpanoramas.org/">SRTM</a> | '
            'Map style: &copy; <a href="https://opentopomap.org">OpenTopoMap</a> (<a href="https://creativecommons.org/licenses/by-sa/3.0/">CC-BY-SA</a>)'
    },
)

ui.run()
```

## Add Markers on Click

您可以使用 `marker` 向地图添加标记。
`center` 参数是纬度和经度的元组。
此演示通过单击地图添加标记。
请注意，“map-click”事件是指地图对象的单击事件，
而“click”事件是指容器 div 的单击事件。

```python
from nicegui import events, ui

m = ui.leaflet(center=(51.505, -0.09))

def handle_click(e: events.GenericEventArguments):
    lat = e.args['latlng']['lat']
    lng = e.args['latlng']['lng']
    m.marker(latlng=(lat, lng))
m.on('map-click', handle_click)

ui.run()
```

## Move Markers

您可以使用 `move` 方法移动标记。

```python
from nicegui import ui

m = ui.leaflet(center=(51.505, -0.09))
marker = m.marker(latlng=m.center)
ui.button('Move marker', on_click=lambda: marker.move(51.51, -0.09))

ui.run()
```

## Vector Layers

Leaflet 支持一组[矢量图层](https://leafletjs.com/reference.html#:~:text=VideoOverlay-,Vector%20Layers,-Path)，例如圆形、多边形等。
这些可以使用 `generic_layer` 方法添加。
我们很乐意审核任何添加更多特定图层以简化使用的拉取请求。

```python
from nicegui import ui

m = ui.leaflet(center=(51.505, -0.09)).classes('h-32')
m.generic_layer(name='circle', args=[m.center, {'color': 'red', 'radius': 300}])

ui.run()
```

## Disable Pan and Zoom

Leaflet 中有[几个选项可以配置地图](https://leafletjs.com/reference.html#map)。
此演示禁用了平移和缩放控件。

```python
from nicegui import ui

options = {
    'zoomControl': False,
    'scrollWheelZoom': False,
    'doubleClickZoom': False,
    'boxZoom': False,
    'keyboard': False,
    'dragging': False,
}
ui.leaflet(center=(51.505, -0.09), options=options)

ui.run()
```

## Draw on Map

您可以启用工具栏在地图上进行绘制。
`draw_control` 可用于配置工具栏。
此演示通过单击地图添加标记和多边形。
通过将“edit”和“remove”设置为 `True`（默认值），您可以启用编辑和删除绘制的形状。

```python
from nicegui import events, ui

def handle_draw(e: events.GenericEventArguments):
    layer_type = e.args['layerType']
    coords = e.args['layer'].get('_latlng') or e.args['layer'].get('_latlngs')
    ui.notify(f'Drawn a {layer_type} at {coords}')

draw_control = {
    'draw': {
        'polygon': True,
        'marker': True,
        'circle': True,
        'rectangle': True,
        'polyline': True,
        'circlemarker': True,
    },
    'edit': {
        'edit': True,
        'remove': True,
    },
}
m = ui.leaflet(center=(51.505, -0.09), draw_control=draw_control)
m.classes('h-96')
m.on('draw:created', handle_draw)
m.on('draw:edited', lambda: ui.notify('Edit completed'))
m.on('draw:deleted', lambda: ui.notify('Delete completed'))

ui.run()
```

## Draw with Custom Options

您可以绘制具有自定义选项的形状，例如笔画颜色和粗细。
要隐藏绘制项目的默认渲染，请将 `hide_drawn_items` 设置为 `True`。

```python
from nicegui import events, ui

def handle_draw(e: events.GenericEventArguments):
    options = {'color': 'red', 'weight': 1}
    m.generic_layer(name='polygon', args=[e.args['layer']['_latlngs'], options])

draw_control = {
    'draw': {
        'polygon': True,
        'marker': False,
        'circle': False,
        'rectangle': False,
        'polyline': False,
        'circlemarker': False,
    },
    'edit': {
        'edit': False,
        'remove': False,
    },
}
m = ui.leaflet(center=(51.5, 0), draw_control=draw_control, hide_drawn_items=True)
m.on('draw:created', handle_draw)

ui.run()
```

## Run Map Methods

您可以使用 `run_map_method` 运行 Leaflet 地图对象的方法。
此演示展示了如何使地图适应整个世界。

```python
from nicegui import ui

m = ui.leaflet(center=(51.505, -0.09)).classes('h-32')
ui.button('Fit world', on_click=lambda: m.run_map_method('fitWorld'))

ui.run()
```

## Run Layer Methods

您可以使用 `run_layer_method` 运行 Leaflet 图层对象的方法。
此演示展示了如何更改标记的不透明度或更改其图标。

```python
from nicegui import ui

m = ui.leaflet(center=(51.505, -0.09)).classes('h-32')
marker = m.marker(latlng=m.center)
ui.button('Hide', on_click=lambda: marker.run_method('setOpacity', 0.3))
ui.button('Show', on_click=lambda: marker.run_method('setOpacity', 1.0))

icon = 'L.icon({iconUrl: "https://leafletjs.com/examples/custom-icons/leaf-green.png"})'
ui.button('Change icon', on_click=lambda: marker.run_method(':setIcon', icon))

ui.run()
```

## Wait for Initialization

您可以使用 `initialized` 方法等待地图初始化。
当您想在创建地图后立即运行诸如适应地图边界之类的方法时，这是必要的。

```python
from nicegui import ui

@ui.page('/')
async def page():
    m = ui.leaflet(zoom=5)
    central_park = m.generic_layer(name='polygon', args=[[
        (40.767809, -73.981249),
        (40.800273, -73.958291),
        (40.797011, -73.949683),
        (40.764704, -73.973741),
    ]])
    await m.initialized()
    bounds = await central_park.run_method('getBounds')
    m.run_map_method('fitBounds', [[bounds['_southWest'], bounds['_northEast']]])

ui.run()
```

## Reference

### Initializer

| 参数             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| center:          | 地图的初始中心位置（纬度/经度，默认为 (0.0, 0.0)）             |
| zoom:            | 地图的初始缩放级别（默认为 13）                               |
| draw_control:    | 是否显示绘制工具栏（默认为 False）                            |
| options:         | 传递给 Leaflet 地图的其他选项（默认为 {}）                    |
| hide_drawn_items: | 是否隐藏地图上绘制的项目（默认为 False）                     |

### Properties

**`center`**`：BindableProperty`

**`classes`**`: 'Classes[Self]'`

该元素的类。

**`is_deleted`**`: 'bool'`

表示该元素是否已被删除。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`props`**`: 'Props[Self]'`

该元素的属性。

**`style`**`: 'Style[Self]'`

该元素的样式。

**`visible`**`: BindableProperty`

**`zoom`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

将资源添加到元素。

| 参数   | 描述                                           |
| ------ | ---------------------------------------------- |
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

| 参数       | 描述           |
| ---------- | -------------- |
| name:      | slot 的名称      |
| template:  | slot 的 Vue 模板 |
| return:    | slot           |

**`ancestors`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的祖先。

| 参数           | 描述                         |
| -------------- | ---------------------------- |
| include_self: | 是否在迭代中包含元素本身 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从这个元素到目标，以及从目标到这个元素。
更新会立即发生，并在值发生变化时发生。
向后绑定优先用于初始同步。

| 参数            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| target_object: | 要绑定到的对象。                                             |
| target_name:    | 要绑定到的属性的名称。                                       |
| forward:        | 在将其应用于目标之前应用于该值的功能。                       |
| backward:       | 在将其应用于此元素之前应用于该值的功能。                     |
| value:          | 如果指定，则仅当目标值等于此值时，元素才可见。             |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从目标对象的 target_name 属性绑定此元素的可见性。

绑定仅以一种方式起作用，从目标到此元素。
更新会立即发生，并在值发生变化时发生。

| 参数            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| target_object: | 要从中绑定的对象。                                           |
| target_name:    | 要从中绑定的属性的名称。                                     |
| backward:       | 在将其应用于此元素之前应用于该值的功能。                     |
| value:          | 如果指定，则仅当目标值等于此值时，元素才可见。             |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅以一种方式起作用，从这个元素到目标。
更新会立即发生，并在值发生变化时发生。

| 参数            | 描述                                           |
| --------------- | ---------------------------------------------- |
| target_object: | 要绑定到的对象。                               |
| target_name:    | 要绑定到的属性的名称。                         |
| forward:        | 在将其应用于目标之前应用于该值的功能。       |

**`clear`**`() -> None`

删除所有子元素。

**`clear_layers`**`() -> None`

从地图中删除所有图层。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或布局。

如果不需要预定义的类，则删除或替换类会很有帮助。
此类所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

| 参数      | 描述                                         |
| --------- | -------------------------------------------- |
| add:      | 空格分隔的类字符串                           |
| remove:   | 要从元素中删除的空格分隔的类字符串         |
| toggle:   | 要切换的空格分隔的类字符串                   |
| replace:  | 用于替换现有类的空格分隔的类字符串         |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类所有元素将共享这些属性。
这些必须在元素实例化之前定义。

如果未指定值，则假定布尔属性为 `True`。

| 参数     | 描述                                             |
| -------- | ------------------------------------------------ |
| add:    | 要添加的布尔值或键值对的空格分隔列表         |
| remove:  | 要删除的属性键的空格分隔列表                   |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式会很有帮助。
此类所有元素将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

| 参数       | 描述                                       |
| ---------- | ------------------------------------------ |
| add:      | 要添加到元素的以分号分隔的样式列表       |
| remove:   | 要从元素中删除的以分号分隔的样式列表     |
| replace:  | 用于替换现有样式的以分号分隔的样式列表   |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

| 参数           | 描述                         |
| -------------- | ---------------------------- |
| include_self: | 是否在迭代中包含元素本身 |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回计算属性。

应该等待此函数，以便正确返回计算属性。

| 参数       | 描述                           |
| ---------- | ------------------------------ |
| prop_name:  | 计算属性的名称                 |
| timeout:   | 等待响应的最大时间（默认为 1 秒） |

**`initialized`**`(timeout: float = 3.0) -> None`

等待直到地图初始化。

| 参数       | 描述                 |
| ---------- | -------------------- |
| timeout:   | 超时时间（以秒为单位，默认为 3 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于识别元素，以便使用 [ElementFilter](/documentation/element_filter) 进行查询
这在测试中大量使用，
但也可以用于减少全局变量的数量或传递依赖项。

| 参数       | 描述                                                       |
| ---------- | ---------------------------------------------------------- |
| markers:  | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

| 参数              | 描述                                     |
| ----------------- | ---------------------------------------- |
| target_container: | 要将元素移动到的容器（默认为父容器）   |
| target_index:     | 目标 slot 中的索引（默认为追加到末尾） |
| target_slot:      | 目标容器中的 slot（默认为默认 slot）     |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

| 参数             | 描述                                                                |
| ---------------- | ------------------------------------------------------------------- |
| type:            | 事件的名称（例如，“click”、“mousedown”或“update:model-value”） |
| handler:         | 事件发生时调用的回调                                                |
| args:            | 发送到事件处理程序的消息中包含的参数（默认为 `None`，表示全部）      |
| throttle:        | 事件发生之间最短的时间（以秒为单位）（默认为 0.0）                |
| leading_events:  | 是否在第一次事件发生时立即触发事件处理程序（默认为 `True`）          |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认为 `True`）          |
| js_handler:      | 在事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认为 `None`） |

**`remove`**`(element: Union[Element, int]) -> None`

删除一个子元素。

| 参数       | 描述                         |
| ---------- | ---------------------------- |
| element:  | 元素实例或其 ID |

**`remove_layer`**`(layer: nicegui.elements.leaflet_layer.Layer) -> None`

从地图中删除一个图层。

**`run_layer_method`**`(layer_id: str, name: str, *args, timeout: float = 1) -> nicegui.awaitable_response.AwaitableResponse`

运行 Leaflet 图层的方法。

如果等待该函数，则返回方法调用的结果。
否则，将执行该方法而不等待响应。

| 参数        | 描述                                                                 |
| ----------- | -------------------------------------------------------------------- |
| layer_id:   | 图层的 ID                                                            |
| name:       | 方法的名称（前缀“:”表示参数是 JavaScript 表达式）                  |
| args:       | 传递给方法的参数                                                     |
| timeout:    | 超时时间（以秒为单位，默认为 1 秒）                                  |
| return:     | AwaitableResponse，可以等待以获取方法调用的结果                     |

**`run_map_method`**`(name: str, *args, timeout: float = 1) -> nicegui.awaitable_response.AwaitableResponse`

运行 Leaflet 地图实例的方法。

有关方法列表，请参阅[Leaflet 文档](https://leafletjs.com/reference.html#map-methods-for-modifying-map-state)。

如果等待该函数，则返回方法调用的结果。
否则，将执行该方法而不等待响应。

| 参数      | 描述                                                                  |
| --------- | --------------------------------------------------------------------- |
| name:     | 方法的名称（前缀“:”表示参数是 JavaScript 表达式）                   |
| args:     | 传递给方法的参数                                                      |
| timeout:  | 超时时间（以秒为单位，默认为 1 秒）                                   |
| return:  | AwaitableResponse，可以等待以获取方法调用的结果                       |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> nicegui.awaitable_response.AwaitableResponse`

**`set_center`**`(center: Tuple[float, float]) -> None`

设置地图的中心位置。

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

| 参数      | 描述                      |
| --------- | ------------------------- |
| visible: | 元素是否应可见。 |

**`set_zoom`**`(zoom: int) -> None`

设置地图的缩放级别。

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

| 参数    | 描述           |
| ------- | -------------- |
| text:   | 工具提示的文本 |

**`update`**`() -> None`

在客户端更新元素。

### Inheritance

- `Element`
- `Visibility`
