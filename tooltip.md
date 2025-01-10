# ui.Tooltip

## Tooltip

这个元素基于 Quasar 的 [QTooltip](https://quasar.dev/vue-components/tooltip) 组件。它可以放置在另一个元素中，以便在悬停时显示额外的信息。

除了传递字符串作为第一个参数外，你还可以将其他元素嵌套在 Tooltip 内部。

|  参数 | 描述 |
| --- | --- |
| text: | tooltip 的内容 (默认: '') |

```python
from nicegui import ui

with ui.button(icon='thumb_up'):
    ui.tooltip('I like this').classes('bg-green')

ui.run()
```

## Tooltip method

除了将 tooltip 元素嵌套在另一个元素中，你还可以使用 `tooltip` 方法。

请注意，使用此方法你不能直接将额外的属性 (props) 或样式应用于 tooltip。如果你需要自定义样式或属性，请嵌套一个 `ui.tooltip` 元素。

```python
from nicegui import ui

ui.label('Tooltips...').tooltip('...are shown on mouse over')

ui.run()
```

## Tooltip with HTML

你可以在 tooltip 中使用 HTML，方法是嵌套一个 `ui.html` 元素。

```python
from nicegui import ui

with ui.label('HTML...'):
    with ui.tooltip():
        ui.html('<b>b</b>, <em>em</em>, <u>u</u>, <s>s</s>')

ui.run()
```

## Tooltip with other content

其他元素（如 `ui.images`）也可以嵌套在 tooltip 中。

```python
from nicegui import ui

with ui.label('Mountains...'):
    with ui.tooltip().classes('bg-transparent'):
        ui.image('https://picsum.photos/id/377/640/360').classes('w-64')

ui.run()
```

## Tooltip on HTML and Markdown

有些元素（如 `ui.html` 和 `ui.markdown`）不支持嵌套元素。在这种情况下，你可以将这些元素嵌套在带有 tooltip 的容器元素中。

```python
from nicegui import ui

with ui.element().tooltip('...with a tooltip!'):
    ui.html('This is <u>HTML</u>...')

ui.run()
```

## Tooltip for the upload element

像 `ui.upload` 这样的组件不直接支持 tooltips。你可以将它们包装在一个 `ui.element` 中，以添加 tooltips 和 props。

```python
from nicegui import ui

with ui.element():
    ui.upload(on_upload=lambda e: ui.notify(f'Uploaded {e.name}')).classes('w-72')
    ui.tooltip('Upload files').props('delay=1000 transition-show=rotate')

ui.run()
```

## Reference

### Initializer

|  参数 | 描述 |
| --- | --- |
| text: | tooltip 的内容 (默认: '') |

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

**`text`**`: BindableProperty`

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
| include_self: | 是否在迭代中包括元素自身 |

**`bind_text`**`(target_object: Any, target_name: str = 'text', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的文本绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标对象，以及从目标对象到该元素。
更新会立即发生，并且在值发生更改时也会发生。
初始同步时，向后绑定优先。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 一个函数，在将值应用于目标之前对其进行应用。 |
| backward: | 一个函数，在将值应用于此元素之前对其进行应用。 |

**`bind_text_from`**`(target_object: Any, target_name: str = 'text', backward: Callable[..., Any] = [...]) -> Self`

从此元素的文本绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标对象到此元素。
更新会立即发生，并且在值发生更改时也会发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| backward: | 一个函数，在将值应用于此元素之前对其进行应用。 |

**`bind_text_to`**`(target_object: Any, target_name: str = 'text', forward: Callable[..., Any] = [...]) -> Self`

将此元素的文本绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标对象。
更新会立即发生，并且在值发生更改时也会发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 一个函数，在将值应用于目标之前对其进行应用。 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标对象，以及从目标对象到该元素。
更新会立即发生，并且在值发生更改时也会发生。
初始同步时，向后绑定优先。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 一个函数，在将值应用于目标之前对其进行应用。 |
| backward: | 一个函数，在将值应用于此元素之前对其进行应用。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从目标对象到此元素。
更新会立即发生，并且在值发生更改时也会发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| backward: | 一个函数，在将值应用于此元素之前对其进行应用。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标对象。
更新会立即发生，并且在值发生更改时也会发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 一个函数，在将值应用于目标之前对其进行应用。 |

**`clear`**`() -> None`

移除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、移除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 空格分隔的类字符串 |
| remove: | 要从元素中移除的空格分隔的类字符串 |
| toggle: | 要切换的空格分隔的类字符串 |
| replace: | 用于替换现有类的空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或其布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些属性。
这些必须在元素实例化之前定义。

如果未指定值，则布尔属性假定为 `True`。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加的布尔值或键值对的空格分隔列表 |
| remove: | 要删除的属性键的空格分隔列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、移除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。
此类的所有元素将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加到元素的以分号分隔的样式列表 |
| remove: | 要从元素中移除的以分号分隔的样式列表 |
| replace: | 用于替换现有样式的以分号分隔的样式列表 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包括元素自身 |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

应等待此函数，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最长时间（默认值：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于通过 [ElementFilter](https://nicegui.io/documentation/element_filter) 识别元素，
该过滤器在测试中大量使用，但也可以用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或包含空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认：父容器） |
| target_index: | 目标槽中的索引（默认：追加到末尾） |
| target_slot: | 目标容器中的槽（默认：默认槽） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅一个事件。

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如：“click”、“mousedown”或“update:model-value”） |
| handler: | 事件发生时调用的回调 |
| args: | 事件消息中包含的参数，发送给事件处理程序（默认值： `None`，表示所有参数） |
| throttle: | 事件发生之间的最小时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值： `True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值： `True`） |
| js_handler: | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值： `None`） |

**`remove`**`(element: Union[Element, int]) -> None`

移除子元素。

|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果等待该函数，则返回方法调用的结果。
否则，将执行该方法而不等待响应。

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 传递给方法的参数 |
| timeout: | 等待响应的最长时间（默认值：1 秒） |

**`set_text`**`(text: str) -> None`

设置此元素的文本。

|  参数 | 描述 |
| --- | --- |
| text: | 新文本。 |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

|  参数 | 描述 |
| --- | --- |
| visible: | 元素是否应该可见。 |

**`tooltip`**`(text: str) -> Self`

向元素添加一个 tooltip。

|  参数 | 描述 |
| --- | --- |
| text: | tooltip 的文本 |

**`update`**`() -> None`

在客户端更新元素。

### Inheritance

*   `TextElement`
*   `Element`
*   `Visibility`
