# ui.Image

## Image

显示图像。
此元素基于 Quasar 的 [QImg](https://quasar.dev/vue-components/img) 组件。

| 参数     | 描述                                                                 |
| -------- | -------------------------------------------------------------------- |
| source: | 图像的来源；可以是 URL、本地文件路径、base64 字符串或 PIL 图像 |

```python
from nicegui import ui

ui.image('https://picsum.photos/id/377/640/360')

ui.run()
```

## Local files

您也可以通过传递图像文件的路径来使用本地图像。

```python
from nicegui import ui

ui.image('website/static/logo.png').classes('w-16')

ui.run()
```

## Base64 string

您还可以使用 Base64 字符串作为图像源。

```python
from nicegui import ui

base64 = 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFCAYAAACNbyblAAAAHElEQVQI12P4//8/w38GIAXDIBKE0DHxgljNBAAO9TXL0Y4OHwAAAABJRU5ErkJggg=='
ui.image(base64).classes('w-2 h-2 m-auto')

ui.run()
```

## PIL image

您还可以使用 PIL 图像作为图像源。

```python
import numpy as np
from nicegui import ui
from PIL import Image

image = Image.fromarray(np.random.randint(0, 255, (100, 100), dtype=np.uint8))
ui.image(image).classes('w-32')

ui.run()
```

## Lottie files

您还可以使用带有动画的 [Lottie 文件](https://lottiefiles.com/)。

```python
from nicegui import ui

ui.add_body_html('<script src="https://unpkg.com/@lottiefiles/lottie-player@latest/dist/lottie-player.js"></script>')

src = 'https://assets1.lottiefiles.com/datafiles/HN7OcWNnoqje6iXIiZdWzKxvLIbfeCGTmvXmEm1h/data.json'
ui.html(f'<lottie-player src="{src}" loop autoplay />').classes('w-full')

ui.run()
```

## Image link

通过将图像包装在 [`ui.link`](https://nicegui.io/documentation/link) 中，图像可以链接到另一个页面。

```python
from nicegui import ui

with ui.link(target='https://github.com/zauberzeug/nicegui'):
    ui.image('https://picsum.photos/id/41/640/360').classes('w-64')

ui.run()
```

## Force reload

您可以通过调用 `force_reload` 方法强制重新加载图像。
它会将时间戳附加到图像 URL，这将使浏览器重新加载图像。

```python
from nicegui import ui

img = ui.image('https://picsum.photos/640/360').classes('w-64')

ui.button('Force reload', on_click=img.force_reload)

ui.run()
```

## Reference

### Initializer

| 参数     | 描述                                                                 |
| -------- | -------------------------------------------------------------------- |
| source: | 图像的来源；可以是 URL、本地文件路径、base64 字符串或 PIL 图像 |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

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

| 参数      | 描述                                    |
| --------- | --------------------------------------- |
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

| 参数       | 描述                  |
| ---------- | --------------------- |
| name:     | slot 的名称           |
| template: | slot 的 Vue 模板    |
| return:   | slot                  |

**`ancestors`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的祖先。

| 参数           | 描述                               |
| -------------- | ---------------------------------- |
| include_self: | 是否在迭代中包含元素本身 |

**`bind_source`**`(target_object: Any, target_name: str = 'source', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的源绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
向后绑定优先用于初始同步。

| 参数            | 描述                                       |
| --------------- | ------------------------------------------ |
| target_object: | 要绑定到的对象。                           |
| target_name:  | 要绑定到的属性的名称。                     |
| forward:      | 在将其应用于目标之前应用于该值的函数。 |
| backward:     | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_source_from`**`(target_object: Any, target_name: str = 'source', backward: Callable[..., Any] = [...]) -> Self`

从此元素的源绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标到此元素。
更新会立即发生，并在值更改时发生。

| 参数            | 描述                                       |
| --------------- | ------------------------------------------ |
| target_object: | 要绑定到的对象。                           |
| target_name:  | 要绑定到的属性的名称。                     |
| backward:     | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_source_to`**`(target_object: Any, target_name: str = 'source', forward: Callable[..., Any] = [...]) -> Self`

将此元素的源绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并在值更改时发生。

| 参数            | 描述                                       |
| --------------- | ------------------------------------------ |
| target_object: | 要绑定到的对象。                           |
| target_name:  | 要绑定到的属性的名称。                     |
| forward:      | 在将其应用于目标之前应用于该值的函数。 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
向后绑定优先用于初始同步。

| 参数            | 描述                                                                        |
| --------------- | --------------------------------------------------------------------------- |
| target_object: | 要绑定到的对象。                                                            |
| target_name:  | 要绑定到的属性的名称。                                                      |
| forward:      | 在将其应用于目标之前应用于该值的函数。                                      |
| backward:     | 在将其应用于此元素之前应用于该值的函数。                                  |
| value:        | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标到此元素。
更新会立即发生，并在值更改时发生。

| 参数            | 描述                                                                        |
| --------------- | --------------------------------------------------------------------------- |
| target_object: | 要绑定到的对象。                                                            |
| target_name:  | 要绑定到的属性的名称。                                                      |
| backward:     | 在将其应用于此元素之前应用于该值的函数。                                  |
| value:        | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并在值更改时发生。

| 参数            | 描述                                       |
| --------------- | ------------------------------------------ |
| target_object: | 要绑定到的对象。                           |
| target_name:  | 要绑定到的属性的名称。                     |
| forward:      | 在将其应用于目标之前应用于该值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

| 参数      | 描述                                      |
| --------- | ----------------------------------------- |
| add:    | 空格分隔的类字符串                          |
| remove: | 要从元素中删除的空格分隔的类字符串 |
| toggle: | 要切换的空格分隔的类字符串                |
| replace: | 要使用的类字符串，替换现有的类，空格分隔   |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或其布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些属性。
这些必须在元素实例化之前定义。

如果未指定值，则布尔属性假定为 `True`。

| 参数      | 描述                                      |
| --------- | ----------------------------------------- |
| add:    | 要添加的布尔值或 key=value 对的空格分隔列表     |
| remove: | 要删除的属性键的空格分隔列表                 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。
此类的所有元素将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

| 参数      | 描述                                             |
| --------- | ------------------------------------------------ |
| add:    | 要添加到元素的分号分隔的样式列表                      |
| remove: | 要从元素中删除的分号分隔的样式列表            |
| replace: | 要使用的样式列表，替换现有的样式，分号分隔 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

| 参数           | 描述                               |
| -------------- | ---------------------------------- |
| include_self: | 是否在迭代中包含元素本身 |

**`force_reload`**`() -> None`

强制图像从源重新加载。

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回计算属性。

此函数应被等待，以便正确返回计算属性。

| 参数         | 描述                             |
| ------------ | -------------------------------- |
| prop_name:  | 计算属性的名称                   |
| timeout:    | 等待响应的最长时间（默认：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于标识用于使用 [ElementFilter](/documentation/element_filter) 查询的元素，
这在测试中大量使用，
但也可以用于减少全局变量的数量或传递依赖项。

| 参数     | 描述                                                     |
| -------- | -------------------------------------------------------- |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

| 参数              | 描述                                                       |
| ----------------- | ---------------------------------------------------------- |
| target_container: | 要将元素移动到的容器（默认：父容器）                          |
| target_index:    | 目标 slot 中的索引（默认：追加到末尾）                        |
| target_slot:     | 目标容器中的 slot（默认：默认 slot）                      |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

| 参数              | 描述                                                                                                          |
| ----------------- | ------------------------------------------------------------------------------------------------------------- |
| type:           | 事件的名称（例如，“click”、“mousedown”或“update:model-value”）                                              |
| handler:         | 事件发生时调用的回调                                                                                          |
| args:            | 发送到事件处理程序中的事件消息中包含的参数（默认值：`None`，表示全部）                                       |
| throttle:       | 事件发生之间的最短时间（以秒为单位）（默认值：0.0）                                                                 |
| leading_events:  | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`）                                                      |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`）                                                    |
| js_handler:      | 在事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`remove`**`(element: Union[Element, int]) -> None`

删除子元素。

| 参数      | 描述                       |
| --------- | -------------------------- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果函数被等待，则返回方法调用的结果。
否则，将执行该方法，而不等待响应。

| 参数      | 描述                                         |
| --------- | -------------------------------------------- |
| name:    | 方法的名称                                   |
| args:    | 传递给方法的参数                               |
| timeout: | 等待响应的最长时间（默认：1 秒） |

**`set_source`**`(source: Union[str, pathlib.Path, ForwardRef('PIL_Image')]) -> None`

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

| 参数      | 描述                     |
| --------- | ------------------------ |
| visible: | 元素是否应该可见。 |

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

| 参数   | 描述        |
| ------ | ----------- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

在客户端更新元素。

### Inheritance

* `SourceElement`
* `Element`
* `Visibility`
