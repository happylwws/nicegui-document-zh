# ui.upload

## File Upload

基于 Quasar 的 [QUploader](https://quasar.dev/vue-components/uploader) 组件。

| 选项 | 描述 |
| --- | --- |
| multiple: | 允许一次上传多个文件 (默认: `False`) |
| max_file_size: | 最大文件大小，以字节为单位 (默认: `0`) |
| max_total_size: | 所有文件的最大总大小，以字节为单位 (默认: `0`) |
| max_files: | 最大文件数 (默认: `0`) |
| on_upload: | 为每个上传的文件执行的回调 |
| on_multi_upload: | 在多个文件上传后执行的回调 |
| on_rejected: | 为每个被拒绝的文件执行的回调 |
| label: | 上传器的标签 (默认: `''`) |
| auto_upload: |  当选择文件时自动上传 (默认: `False`) |

```python
from nicegui import ui

ui.upload(on_upload=lambda e: ui.notify(f'Uploaded {e.name}')).classes('max-w-full')

ui.run()
```

## Upload restrictions

在此演示中，上传限制为最大文件大小 1 MB。
当文件被拒绝时，会显示通知。

```python
from nicegui import ui

ui.upload(on_upload=lambda e: ui.notify(f'Uploaded {e.name}'),
          on_rejected=lambda: ui.notify('Rejected!'),
          max_file_size=1_000_000).classes('max-w-full')

ui.run()
```

## Show file content

在此演示中，上传的 markdown 文件会显示在对话框中。

```python
from nicegui import events, ui

with ui.dialog().props('full-width') as dialog:
    with ui.card():
        content = ui.markdown()

def handle_upload(e: events.UploadEventArguments):
    text = e.content.read().decode('utf-8')
    content.set_content(text)
    dialog.open()

ui.upload(on_upload=handle_upload).props('accept=.md').classes('max-w-full')

ui.run()
```

## Uploading large files

由于底层 Starlette 库中设置的默认文件大小参数，大型文件上传可能会遇到问题。
为了确保顺利上传较大的文件，建议将 Starlette 的 `MultiPartParser` 类中的 `max_file_size` 参数从默认的 `1024 * 1024` (1 MB) 增加到与预期文件大小相符的更高限制。

此演示将 Starlette Multiparser 的 `max_file_size` 增加到 5 MB 以保留在 RAM 中。
此更改允许系统通过将较大的文件保存在 RAM 中来更有效地处理它们，从而避免了将数据写入磁盘上的临时文件并防止上传“卡顿”。

但是，请注意允许用户上传大文件并将它们保留在 RAM 中时，对服务器的潜在影响。

```python
from nicegui import ui
from starlette.formparsers import MultiPartParser

MultiPartParser.max_file_size = 1024 * 1024 * 5  # 5 MB

ui.upload(on_upload=lambda e: ui.notify(f'Uploaded {e.name}')).classes('max-w-full')

ui.run()
```

## Reference

### Initializer

|  选项 | 描述 |
| --- | --- |
| multiple: | 允许一次上传多个文件 (默认: `False`) |
| max_file_size: | 最大文件大小，以字节为单位 (默认: `0`) |
| max_total_size: | 所有文件的最大总大小，以字节为单位 (默认: `0`) |
| max_files: | 最大文件数 (默认: `0`) |
| on_upload: | 为每个上传的文件执行的回调 |
| on_multi_upload: | 在多个文件上传后执行的回调 |
| on_rejected: | 为每个被拒绝的文件执行的回调 |
| label: | 上传器的标签 (默认: `''`) |
| auto_upload: |  当选择文件时自动上传 (默认: `False`) |

### Properties

**`classes`**`: 'Classes[Self]'`

The classes of the element.

**`enabled`**`: BindableProperty`

**`is_deleted`**`: 'bool'`

Whether the element has been deleted.

**`is_ignoring_events`**`: bool`

Return whether the element is currently ignoring events.

**`props`**`: 'Props[Self]'`

The props of the element.

**`style`**`: 'Style[Self]'`

The style of the element.

**`visible`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

Add a resource to the element.

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

Iterate over the ancestors of the element.

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`bind_enabled`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

Bind the enabled state of this element to the target object's target_name property.

The binding works both ways, from this element to the target and from the target to this element.
The update happens immediately and whenever a value changes.
The backward binding takes precedence for the initial synchronization.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的功能。 |
| backward: | 在将其应用于此元素之前应用于该值的功能。 |

**`bind_enabled_from`**`(target_object: Any, target_name: str = 'enabled', backward: Callable[..., Any] = [...]) -> Self`

Bind the enabled state of this element from the target object's target_name property.

The binding works one way only, from the target to this element.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的功能。 |

**`bind_enabled_to`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...]) -> Self`

Bind the enabled state of this element to the target object's target_name property.

The binding works one way only, from this element to the target.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的功能。 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

Bind the visibility of this element to the target object's target_name property.

The binding works both ways, from this element to the target and from the target to this element.
The update happens immediately and whenever a value changes.
The backward binding takes precedence for the initial synchronization.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的功能。 |
| backward: | 在将其应用于此元素之前应用于该值的功能。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

Bind the visibility of this element from the target object's target_name property.

The binding works one way only, from the target to this element.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的功能。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

Bind the visibility of this element to the target object's target_name property.

The binding works one way only, from this element to the target.
The update happens immediately and whenever a value changes.

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的功能。 |

**`clear`**`() -> None`

Remove all child elements.

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

Apply, remove, toggle, or replace default HTML classes.

This allows modifying the look of the element or its layout using [Tailwind](https://tailwindcss.com/) or [Quasar](https://quasar.dev/) classes.

Removing or replacing classes can be helpful if predefined classes are not desired.
All elements of this class will share these HTML classes.
These must be defined before element instantiation.

|  参数 | 描述 |
| --- | --- |
| add: | 空格分隔的类字符串 |
| remove: | 要从元素中删除的空格分隔的类字符串 |
| toggle: | 要切换的空格分隔的类字符串 |
| replace: | 用于替换现有类的空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

Add or remove default props.

This allows modifying the look of the element or its layout using [Quasar](https://quasar.dev/) props.
Since props are simply applied as HTML attributes, they can be used with any HTML element.
All elements of this class will share these props.
These must be defined before element instantiation.

Boolean properties are assumed `True` if no value is specified.

|  参数 | 描述 |
| --- | --- |
| add: | 要添加的布尔值或 key=value 对的空格分隔列表 |
| remove: | 要删除的属性键的空格分隔列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

Apply, remove, or replace default CSS definitions.

Removing or replacing styles can be helpful if the predefined style is not desired.
All elements of this class will share these CSS definitions.
These must be defined before element instantiation.

|  参数 | 描述 |
| --- | --- |
| add: | 要添加到元素的分号分隔的样式列表 |
| remove: | 要从元素中删除的分号分隔的样式列表 |
| replace: | 用于替换现有样式的分号分隔的样式列表 |

**`delete`**`() -> None`

Delete the element and all its children.

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

Iterate over the descendants of the element.

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`disable`**`() -> None`

Disable the element.

**`enable`**`() -> None`

Enable the element.

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

Return a computed property.

This function should be awaited so that the computed property is properly returned.

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最长时间（默认：1 秒） |

**`handle_uploads`**`(uploads: List[starlette.datastructures.UploadFile]) -> None`

Handle the uploaded files.

This method is primarily intended for internal use and for simulating file uploads in tests.

**`mark`**`(*markers: str) -> Self`

Replace markers of the element.

Markers are used to identify elements for querying with [ElementFilter](/documentation/element_filter)
which is heavily used in testing
but can also be used to reduce the number of global variables or passing around dependencies.

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

Move the element to another container.

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认：父容器） |
| target_index: | 目标插槽中的索引（默认：追加到末尾） |
| target_slot: | 目标容器中的插槽（默认：默认插槽） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

Subscribe to an event.

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如，“click”、“mousedown”或“update:model-value”） |
| handler: | 事件发生时调用的回调 |
| args: | 事件消息中包含的发送到事件处理程序的参数（默认值：`None` 表示全部） |
| throttle: | 事件发生之间的最短时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`on_multi_upload`**`(callback: Union[Callable[[nicegui.events.MultiUploadEventArguments], Any], Callable[[], Any]]) -> Self`

Add a callback to be invoked when multiple files have been uploaded.

**`on_rejected`**`(callback: Union[Callable[[nicegui.events.UiEventArguments], Any], Callable[[], Any]]) -> Self`

Add a callback to be invoked when a file is rejected.

**`on_upload`**`(callback: Union[Callable[[nicegui.events.UploadEventArguments], Any], Callable[[], Any]]) -> Self`

Add a callback to be invoked when a file is uploaded.

**`remove`**`(element: Union[Element, int]) -> None`

Remove a child element.

|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`reset`**`() -> None`

Clear the upload queue.

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

Run a method on the client side.

If the function is awaited, the result of the method call is returned.
Otherwise, the method is executed without waiting for a response.

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 传递给方法的参数 |
| timeout: | 等待响应的最长时间（默认：1 秒） |

**`set_enabled`**`(value: bool) -> None`

Set the enabled state of the element.

**`set_visibility`**`(visible: bool) -> None`

Set the visibility of this element.

|  参数 | 描述 |
| --- | --- |
| visible: | 元素是否应该可见。 |

**`tooltip`**`(text: str) -> Self`

Add a tooltip to the element.

|  参数 | 描述 |
| --- | --- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

Update the element on the client side.

### Inheritance

* `DisableableElement`
* `Element`
* `Visibility`
