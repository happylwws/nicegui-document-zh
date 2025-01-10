# ui.scene

## 3D Scene

使用 [three.js](https://threejs.org/) 显示 3D 场景。
目前 NiceGUI 支持盒子、球体、圆柱体/圆锥体、拉伸体、直线、曲线和纹理网格。
对象可以平移、旋转并以不同的颜色、不透明度或线框显示。
它们也可以分组以应用联合运动。

| 参数 | 描述 |
| --- | --- |
| width: | 画布的宽度 |
| height: | 画布的高度 |
| grid: | 是否显示网格（布尔值或元组，包含 `size` 和 `divisions`，用于 [Three.js 的 GridHelper](https://threejs.org/docs/#api/en/helpers/GridHelper)，默认为 100x100） |
| camera: | 相机定义，可以是 `ui.scene.perspective_camera`（默认）或 `ui.scene.orthographic_camera` 的实例 |
| on_click: | 当点击 3D 对象时执行的回调（使用 `click_events` 指定要订阅的事件） |
| click_events: | 要订阅的 JavaScript 点击事件列表（默认: `['click', 'dblclick']`） |
| on_drag_start: | 当拖动 3D 对象时执行的回调 |
| on_drag_end: | 当 3D 对象被放下时执行的回调 |
| drag_constraints: | 用于约束拖动对象位置的逗号分隔 JavaScript 表达式（例如 `x = 0, z = y / 2`） |
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

## Handling Click Events

你可以使用 `ui.scene` 的 `on_click` 参数来处理点击事件。
回调接收一个 `SceneClickEventArguments` 对象，该对象具有以下属性：

- `click_type`: 点击的类型 ("click" 或 "dblclick")。
- `button`: 点击的按钮 (1, 2 或 3)。
- `alt`, `ctrl`, `meta`, `shift`: 是否按下了 alt、ctrl、meta 或 shift 键。
- `hits`: 一个 `SceneClickEventHit` 对象列表，按与相机的距离排序。

`SceneClickEventHit` 对象具有以下属性：

- `object_id`: 被点击对象的 id。
- `object_name`: 被点击对象的名称。
- `x`, `y`, `z`: 点击的 x、y 和 z 坐标。

```python
from nicegui import events, ui

def handle_click(e: events.SceneClickEventArguments):
    hit = e.hits[0]
    name = hit.object_name or hit.object_id
    ui.notify(f'You clicked on the {name} at ({hit.x:.2f}, {hit.y:.2f}, {hit.z:.2f})')

with ui.scene(width=285, height=220, on_click=handle_click) as scene:
    scene.sphere().move(x=-1, z=1).with_name('sphere')
    scene.box().move(x=1, z=1).with_name('box')

ui.run()
```

## Context menu for 3D objects

这个演示展示了如何为 3D 对象创建上下文菜单。
通过将 `click_events` 参数设置为 `['contextmenu']`，右键单击时将调用 `handle_click` 函数。
它会清除上下文菜单并根据单击的对象添加项目。

```python
from nicegui import events, ui

def handle_click(e: events.SceneClickEventArguments) -> None:
    context_menu.clear()
    name = next((hit.object_name for hit in e.hits if hit.object_name), None)
    with context_menu:
        if name == 'sphere':
            ui.item('SPHERE').classes('font-bold')
            ui.menu_item('inspect')
            ui.menu_item('open')
        if name == 'box':
            ui.item('BOX').classes('font-bold')
            ui.menu_item('rotate')
            ui.menu_item('move')

with ui.element():
    context_menu = ui.context_menu()
    with ui.scene(width=285, height=220, on_click=handle_click,
                  click_events=['contextmenu']) as scene:
        scene.sphere().move(x=-1, z=1).with_name('sphere')
        scene.box().move(x=1, z=1).with_name('box')

ui.run()
```

## Draggable objects

你可以使用 `.draggable` 方法使对象可拖动。
`ui.scene` 有一个可选的 `on_drag_start` 和 `on_drag_end` 参数来处理拖动事件。
回调接收一个 `SceneDragEventArguments` 对象，该对象具有以下属性：

- `type`: 拖动事件的类型 ("dragstart" 或 "dragend")。
- `object_id`: 被拖动对象的 id。
- `object_name`: 被拖动对象的名称。
- `x`, `y`, `z`: 被拖动对象的 x、y 和 z 坐标。

你还可以使用 `drag_constraints` 参数来设置逗号分隔的 JavaScript 表达式，以约束拖动对象的位置。

```python
from nicegui import events, ui

def handle_drag(e: events.SceneDragEventArguments):
    ui.notify(f'You dropped the sphere at ({e.x:.2f}, {e.y:.2f}, {e.z:.2f})')

with ui.scene(width=285, height=220,
              drag_constraints='z = 1', on_drag_end=handle_drag) as scene:
    sphere = scene.sphere().move(z=1).draggable()

ui.switch('draggable sphere',
          value=sphere.draggable_,
          on_change=lambda e: sphere.draggable(e.value))

ui.run()
```

## Subscribe to the drag event

默认情况下，只有在拖动结束时才会更新可拖动对象，以避免性能问题。
但是，你可以显式订阅 "drag" 事件以获取即时更新。
在此演示中，我们根据两个可拖动球体的位置更新一个盒子的位置和大小。

```python
from nicegui import events, ui

with ui.scene(width=285, drag_constraints='z=0') as scene:
    box = scene.box(1, 1, 0.2).move(0, 0).material('Orange')
    sphere1 = scene.sphere(0.2).move(0.5, -0.5).material('SteelBlue').draggable()
    sphere2 = scene.sphere(0.2).move(-0.5, 0.5).material('SteelBlue').draggable()

def handle_drag(e: events.GenericEventArguments) -> None:
    x1 = sphere1.x if e.args['object_id'] == sphere2.id else e.args['x']
    y1 = sphere1.y if e.args['object_id'] == sphere2.id else e.args['y']
    x2 = sphere2.x if e.args['object_id'] == sphere1.id else e.args['x']
    y2 = sphere2.y if e.args['object_id'] == sphere1.id else e.args['y']
    box.move((x1 + x2) / 2, (y1 + y2) / 2).scale(x2 - x1, y2 - y1, 1)
scene.on('drag', handle_drag)

ui.run()
```

## Rendering point clouds

你可以使用 `point_cloud` 方法渲染点云。
`points` 参数是点坐标列表，`colors` 参数是 RGB 颜色列表 (0..1)。
你可以使用它的 `set_points()` 方法更新云。

```python
import numpy as np
from nicegui import ui

def generate_data(frequency: float = 1.0):
    x, y = np.meshgrid(np.linspace(-3, 3), np.linspace(-3, 3))
    z = np.sin(x * frequency) * np.cos(y * frequency) + 1
    points = np.dstack([x, y, z]).reshape(-1, 3)
    colors = points / [6, 6, 2] + [0.5, 0.5, 0]
    return points, colors

with ui.scene().classes('w-full h-64') as scene:
    points, colors = generate_data()
    point_cloud = scene.point_cloud(points, colors, point_size=0.1)

ui.slider(min=0.1, max=3, step=0.1, value=1) \
    .on_value_change(lambda e: point_cloud.set_points(*generate_data(e.value)))

ui.run()
```

## Wait for Initialization

你可以使用 `initialized` 方法等待场景初始化。
此演示在场景完全加载后动画化相机移动。

```python
from nicegui import ui

@ui.page('/')
async def page():
    with ui.scene(width=285, height=220) as scene:
        scene.sphere()
        await scene.initialized()
        scene.move_camera(x=1, y=-1, z=1.5, duration=2)

ui.run()
```

## Scene View

使用 [three.js](https://threejs.org/) 显示 3D 场景的附加视图。
此组件只能显示场景，而不能修改它。
但是，你可以独立移动相机。

当前限制：不支持 2D 和 3D 文本对象，并且不会在场景视图中显示。

| 参数 | 描述 |
| --- | --- |
| scene: | 将在画布上显示的场景 |
| width: | 画布的宽度 |
| height: | 画布的高度 |
| camera: | 相机定义，可以是 `ui.scene.perspective_camera`（默认）或 `ui.scene.orthographic_camera` 的实例 |
| on_click: | 当点击 3D 对象时执行的回调 |

```python
from nicegui import ui

with ui.grid(columns=2).classes('w-full'):
    with ui.scene().classes('h-64 col-span-2') as scene:
        teapot = 'https://upload.wikimedia.org/wikipedia/commons/9/93/Utah_teapot_(solid).stl'
        scene.stl(teapot).scale(0.3)

    with ui.scene_view(scene).classes('h-32') as scene_view1:
        scene_view1.move_camera(x=1, y=-3, z=5)

    with ui.scene_view(scene).classes('h-32') as scene_view2:
        scene_view2.move_camera(x=0, y=4, z=3)

ui.run()
```

## Camera Parameters

你可以使用 `ui.scene` 的 `camera` 参数来使用自定义相机。
这允许你设置透视相机的视野或正交相机的大小。

```python
from nicegui import ui

with ui.scene(camera=ui.scene.orthographic_camera(size=2)) \
        .classes('w-full h-64') as scene:
    scene.box()

ui.run()
```

## Get current camera pose

使用 `get_camera` 方法，你可以获得当前相机参数的字典，如位置、旋转、视野等。
此演示展示了如何使一个球体不断向相机移动。
尝试移动相机，看看球体如何跟随它。

```python
from nicegui import ui

with ui.scene().classes('w-full h-64') as scene:
    ball = scene.sphere()

async def move():
    camera = await scene.get_camera()
    if camera is not None:
        ball.move(x=0.95 * ball.x + 0.05 * camera['position']['x'],
                  y=0.95 * ball.y + 0.05 * camera['position']['y'],
                  z=1.0)
ui.timer(0.1, move)

ui.run()
```

## Custom Background

你可以使用 `ui.scene` 的 `background_color` 参数设置自定义背景颜色。

```python
from nicegui import ui

with ui.scene(background_color='#222').classes('w-full h-64') as scene:
    scene.box()

ui.run()
```

## Custom Grid

你可以使用 `ui.scene` 的 `grid` 参数设置自定义网格参数。
它接受一个由两个整数组成的元组，第一个用于网格大小，第二个用于分割数。

```python
from nicegui import ui

with ui.scene(grid=(3, 2)).classes('w-full h-64') as scene:
    scene.sphere()

ui.run()
```

## Custom Composed 3D Objects

此演示创建了一个自定义类，用于可视化具有彩色 X、Y 和 Z 轴的坐标系。
这可以替代默认的 `axes_helper` 对象。

```python
import math
from nicegui import ui

class CoordinateSystem(ui.scene.group):

    def __init__(self, name: str, *, length: float = 1.0) -> None:
        super().__init__()

        with self:
            for label, color, rx, ry, rz in [
                ('x', '#ff0000', 0, 0, -math.pi / 2),
                ('y', '#00ff00', 0, 0, 0),
                ('z', '#0000ff', math.pi / 2, 0, 0),
            ]:
                with ui.scene.group().rotate(rx, ry, rz):
                    ui.scene.cylinder(0.02 * length, 0.02 * length, 0.8 * length) \
                        .move(y=0.4 * length).material(color)
                    ui.scene.cylinder(0, 0.1 * length, 0.2 * length) \
                        .move(y=0.9 * length).material(color)
                    ui.scene.text(label, style=f'color: {color}') \
                        .move(y=1.1 * length)
            ui.scene.text(name, style='color: #808080')

with ui.scene().classes('w-full h-64'):
    CoordinateSystem('origin')
    CoordinateSystem('custom frame').move(-2, -2, 1).rotate(0.1, 0.2, 0.3)

ui.run()
```

## Attaching/detaching objects

要向组添加或从中删除对象，可以使用 `attach` 和 `detach` 方法。
对象的 position 和 rotation 被保留，因此对象不会在空间中移动。
但请注意，缩放不会保留。
如果父对象或对象本身被缩放，则对象形状和位置可能会更改。

```python
import math
import time
from nicegui import ui

with ui.scene().classes('w-full h-64') as scene:
    with scene.group() as group:
        a = scene.box().move(-2)
        b = scene.box().move(0)
        c = scene.box().move(2)

ui.timer(0.1, lambda: group.move(y=math.sin(time.time())).rotate(0, 0, time.time()))
ui.button('Detach', on_click=a.detach)
ui.button('Attach', on_click=lambda: a.attach(group))

ui.run()
```

## Reference

### Initializer

| 参数 | 描述 |
| --- | --- |
| width: | 画布的宽度 |
| height: | 画布的高度 |
| grid: | 是否显示网格（布尔值或元组，包含 `size` 和 `divisions`，用于 [Three.js 的 GridHelper](https://threejs.org/docs/#api/en/helpers/GridHelper)，默认为 100x100） |
| camera: | 相机定义，可以是 `ui.scene.perspective_camera`（默认）或 `ui.scene.orthographic_camera` 的实例 |
| on_click: | 当点击 3D 对象时执行的回调（使用 `click_events` 指定要订阅的事件） |
| click_events: | 要订阅的 JavaScript 点击事件列表（默认: `['click', 'dblclick']`） |
| on_drag_start: | 当拖动 3D 对象时执行的回调 |
| on_drag_end: | 当 3D 对象被放下时执行的回调 |
| drag_constraints: | 用于约束拖动对象位置的逗号分隔 JavaScript 表达式（例如 `x = 0, z = y / 2`） |
| background_color: | 场景的背景颜色 (默认: "#eee") |

### Properties

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

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

将资源添加到元素。

|  参数 | 描述 |
| --- | --- |
| path: | 资源路径 (例如：包含 CSS 和 JavaScript 文件的文件夹) |

**`add_slot`**`(name: str, template: Optional[str] = None) -> Slot`

向元素添加一个 slot。

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

绑定是双向的，从此元素到目标，以及从目标到此元素。
更新会立即发生，并在值发生更改时发生。
向后绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 一个函数，在将值应用于目标之前对其进行应用。 |
| backward: | 一个函数，在将值应用于此元素之前对其进行应用。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此目标对象的 target_name 属性绑定此元素的可见性。

绑定仅以一种方式工作，从目标到此元素。
更新会立即发生，并在值发生更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| backward: | 一个函数，在将值应用于此元素之前对其进行应用。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从此元素到目标。
更新会立即发生，并在值发生更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 一个函数，在将值应用于目标之前对其进行应用。 |

**`clear`**`() -> None`

从场景中删除所有对象。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 空格分隔的类字符串 |
| remove: | 要从元素中删除的空格分隔的类字符串 |
| toggle: | 要切换的空格分隔的类字符串 |
| replace: | 用于替换现有类的空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认 props。

这允许使用 [Quasar](https://quasar.dev/) props 修改元素的外观或其布局。
由于 props 只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些 props。
这些必须在元素实例化之前定义。

如果未指定值，则布尔属性假定为 `True`。

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
| add: | 要添加到元素的分号分隔的样式列表 |
| remove: | 要从元素中删除的分号分隔的样式列表 |
| replace: | 用于替换现有样式的分号分隔的样式列表 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`delete_objects`**`(predicate: Callable[[nicegui.elements.scene_object3d.Object3D], bool] = [...]) -> None`

从场景中删除对象。

|  参数 | 描述 |
| --- | --- |
| predicate: | 一个函数，对于应该删除的对象返回 `True` |

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`get_camera`**`() -> Dict[str, Any]`

获取当前相机参数。

与 `camera` 属性相反，此方法的结果包括用户在浏览器中导航场景时导致的当前相机姿态。

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

此函数应被等待，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最长时间（默认：1 秒） |

**`initialized`**`() -> None`

等待直到场景初始化完成。

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于使用 [ElementFilter](/documentation/element_filter) 查询元素，
这在测试中大量使用，
但也可用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 将元素移动到的容器（默认：父容器） |
| target_index: | 目标槽中的索引（默认：追加到末尾） |
| target_slot: | 目标容器中的槽（默认：默认槽） |

**`move_camera`**`(x: Optional[float] = None, y: Optional[float] = None, z: Optional[float] = None, look_at_x: Optional[float] = None, look_at_y: Optional[float] = None, look_at_z: Optional[float] = None, up_x: Optional[float] = None, up_y: Optional[float] = None, up_z: Optional[float] = None, duration: float = 0.5) -> None`

将相机移动到新位置。

|  参数 | 描述 |
| --- | --- |
| x: | 相机 x 位置 |
| y: | 相机 y 位置 |
| z: | 相机 z 位置 |
| look_at_x: | 相机注视 x 位置 |
| look_at_y: | 相机注视 y 位置 |
| look_at_z: | 相机注视 z 位置 |
| up_x: | 相机向上向量的 x 分量 |
| up_y: | 相机向上向量的 y 分量 |
| up_z: | 相机向上向量的 z 分量 |
| duration: | 移动的持续时间，以秒为单位（默认值：`0.5`） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅一个事件。

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如 "click"、"mousedown" 或 "update:model-value"） |
| handler: | 事件发生时调用的回调函数 |
| args: | 事件消息中包含的参数，发送到事件处理程序（默认值：`None`，表示全部） |
| throttle: | 事件发生之间的最小时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`on_click`**`(callback: Union[Callable[[nicegui.events.SceneClickEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个回调，在点击 3D 对象时调用。

**`on_drag_end`**`(callback: Union[Callable[[nicegui.events.SceneDragEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个回调，在放下 3D 对象时调用。

**`on_drag_start`**`(callback: Union[Callable[[nicegui.events.SceneDragEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个回调，在拖动 3D 对象时调用。

`@staticmethod`
**`orthographic_camera`**`(size: float = 10, near: float = 0.1, far: float = 1000) -> nicegui.elements.scene.SceneCamera`

创建一个正交相机。

大小定义了视图体积的垂直大小，即顶部和底部裁剪平面之间的距离。
左侧和右侧裁剪平面设置为使宽高比与视口匹配。

|  参数 | 描述 |
| --- | --- |
| size: | 视图体积的垂直大小 |
| near: | 近裁剪平面 |
| far: | 远裁剪平面 |

`@staticmethod`
**`perspective_camera`**`(fov: float = 75, near: float = 0.1, far: float = 1000) -> nicegui.elements.scene.SceneCamera`

创建一个透视相机。

|  参数 | 描述 |
| --- | --- |
| fov: | 垂直视角，以度为单位 |
| near: | 近裁剪平面 |
| far: | 远裁剪平面 |

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

更新客户端上的元素。

### Inheritance

- `Element`
- `Visibility`
