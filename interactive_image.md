# ui.interactive_image

## Interactive Image

创建一个带有 SVG 覆盖层的图像，该覆盖层处理鼠标事件并产生图像坐标。
它也是非闪烁图像更新的最佳选择。
如果源 URL 的更改速度快于浏览器加载图像的速度，则会直接跳过某些图像。
因此，重复更新图像源将自动适应可用的带宽。
有关示例，请参见 [OpenCV Webcam](https://github.com/zauberzeug/nicegui/tree/main/examples/opencv_webcam/main.py)。

鼠标事件处理程序被调用时会带有鼠标事件参数，其中包含：

*   `type` (JavaScript 事件的名称),
*   `image_x` 和 `image_y` (像素为单位的图像坐标),
*   `button` 和 `buttons` (来自 JavaScript 事件的鼠标按钮编号), 以及
*   `alt`, `ctrl`, `meta` 和 `shift` (来自 JavaScript 事件的修饰键)。

您还可以传递宽度和高度的元组而不是图像源。
这将创建一个具有给定大小的空图像。

| 参数 | 描述 |
| --- | --- |
| source: | 图像的来源；可以是 URL、本地文件路径、base64 字符串或仅是图像大小 |
| content: | 应该覆盖的 SVG 内容；视口与图像具有相同的尺寸 |
| size: | 图像的大小（宽度，高度），以像素为单位；仅当未设置 source 时使用 |
| on_mouse: | 鼠标事件的回调（包含以像素为单位的图像坐标 image_x 和 image_y） |
| events: | 要订阅的 JavaScript 事件列表（默认值：`['click']`） |
| cross: | 是否显示十字准星或颜色字符串（默认值：`False`） |

```python
from nicegui import events, ui

def mouse_handler(e: events.MouseEventArguments):
    color = 'SkyBlue' if e.type == 'mousedown' else 'SteelBlue'
    ii.content += f'<circle cx="{e.image_x}" cy="{e.image_y}" r="15" fill="none" stroke="{color}" stroke-width="4" />'
    ui.notify(f'{e.type} at ({e.image_x:.1f}, {e.image_y:.1f})')

src = 'https://picsum.photos/id/565/640/360'
ii = ui.interactive_image(src, on_mouse=mouse_handler, events=['mousedown', 'mouseup'], cross=True)

ui.run()
```

## Nesting elements

您可以在交互式图像中嵌套元素。
使用 Tailwind 类（例如“absolute top-0 left-0”）将标签绝对定位于图像。
当然，这也可以使用纯 CSS 完成。

```python
from nicegui import ui

with ui.interactive_image('https://picsum.photos/id/147/640/360'):
    ui.button(on_click=lambda: ui.notify('thumbs up'), icon='thumb_up') \
        .props('flat fab color=white') \
        .classes('absolute bottom-0 left-0 m-2')

ui.run()
```

## Force reload

您可以通过调用 `force_reload` 方法强制重新加载图像。
它会在图像 URL 中附加一个时间戳，这将使浏览器重新加载图像。

```python
from nicegui import ui

img = ui.interactive_image('https://picsum.photos/640/360').classes('w-64')

ui.button('Force reload', on_click=img.force_reload)

ui.run()
```

## Blank canvas

您还可以创建具有给定大小的空白画布。
如果您想在不加载背景图像的情况下绘制某些内容，这将非常有用。

```python
from nicegui import ui

ui.interactive_image(
    size=(800, 600), cross=True,
    on_mouse=lambda e: e.sender.set_content(f'''
        <circle cx="{e.image_x}" cy="{e.image_y}" r="50" fill="orange" />
    '''),
).classes('w-64 bg-blue-50')

ui.run()
```

## Loaded event

您可以监听 "loaded" 事件，以了解图像何时已加载。

```python
import time
from nicegui import ui

ii = ui.interactive_image('https://picsum.photos/640/360')
ii.on('loaded', lambda e: ui.notify(f'loaded {e.args}'))
ui.button('Change Source', on_click=lambda: ii.set_source(f'https://picsum.photos/640/360?time={time.time()}'))

ui.run()
```

## Crosshairs

您可以通过传递 `cross=True` 来显示十字准星。
您还可以通过传递颜色字符串来更改十字准星的颜色。

或者，您可以使用 `add_slot` 方法添加带有您自己的 SVG 模板的自定义 "cross" 插槽。
`props.x` 和 `props.y` 变量将在模板中可用，表示十字准星的位置。

```python
from nicegui import ui

ui.interactive_image('https://picsum.photos/id/565/640/360', cross='red')

ui.interactive_image('https://picsum.photos/id/565/640/360').add_slot('cross', '''
    <circle :cx="props.x" :cy="props.y" r="30" stroke="red" fill="none" />
    <line :x1="props.x - 30" :y1="props.y" :x2="props.x + 30" :y2="props.y" stroke="red" />
    <line :x1="props.x" :y1="props.y - 30" :x2="props.x" :y2="props.y + 30" stroke="red" />
''')

ui.run()
```

## SVG events

您可以使用带有 "svg:" 前缀的 `on` 方法订阅 SVG 元素的事件。
确保为要接收事件的 SVG 元素设置 `pointer-events="all"`。

目前支持以下 SVG 事件：

*   pointermove
*   pointerdown
*   pointerup
*   pointerover
*   pointerout
*   pointerenter
*   pointerleave
*   pointercancel

```python
from nicegui import ui

ui.interactive_image('https://picsum.photos/id/565/640/360', cross=True, content='''
    <rect id="A" x="85" y="70" width="80" height="60" fill="none" stroke="red" pointer-events="all" cursor="pointer" />
    <rect id="B" x="180" y="70" width="80" height="60" fill="none" stroke="red" pointer-events="all" cursor="pointer" />
''').on('svg:pointerdown', lambda e: ui.notify(f'SVG clicked: {e.args}'))

ui.run()
```

## Reference

### Initializer

| 参数 | 描述 |
| --- | --- |
| source: | 图像的来源；可以是 URL、本地文件路径、base64 字符串或仅是图像大小 |
| content: | 应该覆盖的 SVG 内容；视口与图像具有相同的尺寸 |
| size: | 图像的大小（宽度，高度），以像素为单位；仅当未设置 source 时使用 |
| on_mouse: | 鼠标事件的回调（包含以像素为单位的图像坐标 image_x 和 image_y） |
| events: | 要订阅的 JavaScript 事件列表（默认值：`['click']`） |
| cross: | 是否显示十字准星或颜色字符串（默认值：`False`） |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`content`**`: BindableProperty`

**`is_deleted`**`: 'bool'`

元素是否已被删除。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`props`**`: 'Props[Self]'`

元素的属性。

**`source`**`: BindableProperty`

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
| include_self: | 是否在迭代中包含元素本身 |

**`bind_content`**`(target_object: Any, target_name: str = 'content', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的内容绑定到目标对象的 target_name 属性。

绑定是双向的，从这个元素到目标，以及从目标到这个元素。
更新会立即发生，并在值发生更改时进行。
反向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于值的函数。 |
| backward: | 在将其应用于此元素之前应用于值的函数。 |

**`bind_content_from`**`(target_object: Any, target_name: str = 'content', backward: Callable[..., Any] = [...]) -> Self`

从目标对象的 target_name 属性绑定此元素的内容。

绑定仅以一种方式工作，从目标到此元素。
更新会立即发生，并在值发生更改时进行。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于值的函数。 |

**`bind_content_to`**`(target_object: Any, target_name: str = 'content', forward: Callable[..., Any] = [...]) -> Self`

将此元素的内容绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从这个元素到目标。
更新会立即发生，并在值发生更改时进行。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于值的函数。 |

**`bind_source`**`(target_object: Any, target_name: str = 'source', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的 source 绑定到目标对象的 target_name 属性。

绑定是双向的，从这个元素到目标，以及从目标到这个元素。
更新会立即发生，并在值发生更改时进行。
反向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于值的函数。 |
| backward: | 在将其应用于此元素之前应用于值的函数。 |

**`bind_source_from`**`(target_object: Any, target_name: str = 'source', backward: Callable[..., Any] = [...]) -> Self`

从目标对象的 target_name 属性绑定此元素的 source。

绑定仅以一种方式工作，从目标到此元素。
更新会立即发生，并在值发生更改时进行。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于值的函数。 |

**`bind_source_to`**`(target_object: Any, target_name: str = 'source', forward: Callable[..., Any] = [...]) -> Self`

将此元素的 source 绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从这个元素到目标。
更新会立即发生，并在值发生更改时进行。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于值的函数。 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从这个元素到目标，以及从目标到这个元素。
更新会立即发生，并在值发生更改时进行。
反向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于值的函数。 |
| backward: | 在将其应用于此元素之前应用于值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此目标对象的 target_name 属性绑定此元素的可见性。

绑定仅以一种方式工作，从目标到此元素。
更新会立即发生，并在值发生更改时进行。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从这个元素到目标。
更新会立即发生，并在值发生更改时进行。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或布局。

如果不需要预定义的类，删除或替换类可能会有所帮助。
此类的所有元素都将共享这些 HTML 类。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 空格分隔的类字符串 |
| remove: | 要从元素中删除的空格分隔的类字符串 |
| toggle: | 要切换的空格分隔的类字符串 |
| replace: | 用于代替现有类的空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素都将共享这些属性。
这些必须在元素实例化之前定义。

如果未指定值，则布尔属性假定为 `True`。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加的布尔值或键=值对的空格分隔列表 |
| remove: | 要删除的属性键的空格分隔列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，删除或替换样式可能会有所帮助。
此类的所有元素都将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加到元素的以分号分隔的样式列表 |
| remove: | 要从元素中删除的以分号分隔的样式列表 |
| replace: | 用于代替现有样式的以分号分隔的样式列表 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`force_reload`**`() -> None`

强制从源重新加载图像。

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回计算属性。

应该等待此函数，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最长时间（默认值：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于识别用于使用 [ElementFilter](/documentation/element_filter) 查询的元素，
该过滤器在测试中大量使用，
但也可以用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认值：父容器） |
| target_index: | 目标槽内的索引（默认值：追加到末尾） |
| target_slot: | 目标容器内的槽（默认值：默认槽） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如“click”、“mousedown”或“update:model-value”） |
| handler: | 事件发生时调用的回调 |
| args: | 事件消息中包含的发送到事件处理程序的参数（默认值：`None`，表示全部） |
| throttle: | 事件发生之间的最短时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 在事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`on_mouse`**`(on_mouse: Handler[MouseEventArguments]) -> Self`

添加一个在发生鼠标事件时调用的回调。

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

**`set_content`**`(content: str) -> None`

设置此元素的内容。

|  参数 | 描述 |
| --- | --- |
| content: | 新内容。 |

**`set_source`**`(source: "Union[str, Path, 'PIL_Image']") -> None`

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

*   `SourceElement`
*   `ContentElement`
*   `Element`
*   `Visibility`
