# ui.json_editor

## JSONEditor

这是一个使用 [JSONEditor](https://github.com/josdejong/svelte-jsoneditor) 创建 JSON 编辑器的元素。
可以通过更改 `properties` 属性将更新推送到编辑器。
数据更改后，调用 `update` 方法刷新编辑器。

| 选项 | 描述 |
| --- | --- |
| properties: | JSONEditor 属性的字典 |
| on_select: | 当某些内容被选中时调用的回调函数 |
| on_change: | 当内容更改时调用的回调函数 |
| schema: | 可选的 [JSON schema](https://json-schema.org/)，用于验证正在编辑的数据 |

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

## Validation

您可以使用 `schema` 参数来定义一个 [JSON schema](https://json-schema.org/)，用于验证正在编辑的数据。
在此演示中，如果数据与 schema 不匹配，编辑器将发出警告：

*   `id` 必须是整数
*   `name` 必须是字符串
*   `price` 必须是大于 0 的数字

```python
from nicegui import ui

schema = {
    'type': 'object',
    'properties': {
        'id': {
            'type': 'integer',
        },
        'name': {
            'type': 'string',
        },
        'price': {
            'type': 'number',
            'exclusiveMinimum': 0,
        },
    },
    'required': ['id', 'name', 'price'],
}
data = {
    'id': 42,
    'name': 'Banana',
    'price': 15.0,
}
ui.json_editor({'content': {'json': data}}, schema=schema)

ui.run()
```

## Run methods

您可以使用 `run_editor_method` 方法运行 JSONEditor 实例的方法。
此演示展示了如何展开和折叠所有节点以及如何获取当前数据。

方法名称 "expand" 前面的冒号 ":" 表示值 "path => true" 是一个 JavaScript 表达式，
在传递给方法之前在客户端上进行计算。

请注意，仅在页面函数中支持从客户端请求数据，而不支持共享的自动索引页面。

```python
from nicegui import ui

@ui.page('/')
def page():
    json = {
        'Name': 'Alice',
        'Age': 42,
        'Address': {
            'Street': 'Main Street',
            'City': 'Wonderland',
        },
    }
    editor = ui.json_editor({'content': {'json': json}})

    ui.button('Expand', on_click=lambda: editor.run_editor_method(':expand', 'path => true'))
    ui.button('Collapse', on_click=lambda: editor.run_editor_method(':expand', 'path => false'))
    ui.button('Readonly', on_click=lambda: editor.run_editor_method('updateProps', {'readOnly': True}))

    async def get_data() -> None:
        data = await editor.run_editor_method('get')
        ui.notify(data)
    ui.button('Get Data', on_click=get_data)

ui.run()
```

## Reference

### Initializer

| 选项 | 描述 |
| --- | --- |
| properties: | JSONEditor 属性的字典 |
| on_select: | 当某些内容被选中时调用的回调函数 |
| on_change: | 当内容更改时调用的回调函数 |
| schema: | 可选的 [JSON schema](https://json-schema.org/)，用于验证正在编辑的数据 |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`is_deleted`**`: 'bool'`

元素是否已被删除。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`properties`**`: Dict`

属性字典。

**`props`**`: 'Props[Self]'`

元素的 props。

**`style`**`: 'Style[Self]'`

元素的样式。

**`visible`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

向元素添加资源。

| 参数      | 描述                     |
| --------- | ------------------------ |
| path:    | 资源路径 (例如：包含 CSS 和 JavaScript 文件的文件夹) |

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

| 参数       | 描述         |
| ---------- | ------------ |
| name:      | slot 的名称 |
| template: | slot 的 Vue 模板 |
| return:   | slot         |

**`ancestors`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的祖先。

| 参数            | 描述                                  |
| --------------- | ------------------------------------- |
| include_self: | 是否在迭代中包含元素本身 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在每次值更改时发生。
对于初始同步，后向绑定优先。

| 参数            | 描述                                                     |
| --------------- | -------------------------------------------------------- |
| target_object: | 要绑定到的对象。                                           |
| target_name:   | 要绑定到的属性的名称。                                       |
| forward:       | 在将值应用于目标之前应用于该值的函数。                               |
| backward:      | 在将值应用于此元素之前应用于该值的函数。                               |
| value:         | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从目标到该元素。
更新会立即发生，并在每次值更改时发生。

| 参数            | 描述                                                     |
| --------------- | -------------------------------------------------------- |
| target_object: | 要从中绑定的对象。                                           |
| target_name:   | 要从中绑定的属性的名称。                                       |
| backward:      | 在将值应用于此元素之前应用于该值的函数。                               |
| value:         | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从该元素到目标。
更新会立即发生，并在每次值更改时发生。

| 参数            | 描述                                 |
| --------------- | ------------------------------------ |
| target_object: | 要绑定到的对象。                         |
| target_name:   | 要绑定到的属性的名称。                     |
| forward:       | 在将值应用于目标之前应用于该值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类来修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类可能很有帮助。
此类的所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

| 参数      | 描述                                       |
| --------- | ------------------------------------------ |
| add:      | 空格分隔的类字符串                         |
| remove:   | 要从元素中删除的空格分隔的类字符串         |
| toggle:   | 要切换的空格分隔的类字符串                   |
| replace:  | 用于替换现有类的空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认 props。

这允许使用 [Quasar](https://quasar.dev/) props 修改元素的外观或其布局。
由于 props 只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些 props。
这些必须在元素实例化之前定义。

如果未指定值，则假定布尔属性为 `True`。

| 参数      | 描述                                                       |
| --------- | ---------------------------------------------------------- |
| add:      | 空格分隔的布尔值或要添加的 key=value 对的列表     |
| remove:   | 要删除的属性键的空格分隔列表                               |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能很有帮助。
此类的所有元素将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

| 参数      | 描述                                                       |
| --------- | ---------------------------------------------------------- |
| add:      | 要添加到元素的以分号分隔的样式列表                         |
| remove:   | 要从元素中删除的以分号分隔的样式列表                       |
| replace:  | 用于替换现有样式的以分号分隔的样式列表 |

**`delete`**`() -> None`

删除该元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

| 参数            | 描述                                  |
| --------------- | ------------------------------------- |
| include_self: | 是否在迭代中包含元素本身 |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回计算的属性。

应该等待此函数，以便正确返回计算的属性。

| 参数        | 描述                            |
| ----------- | ------------------------------- |
| prop_name:  | 计算的属性的名称                    |
| timeout:   | 等待响应的最长时间（默认：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于标识要使用 [ElementFilter](/documentation/element_filter) 查询的元素
这在测试中大量使用，
但也可以用于减少全局变量的数量或传递依赖项。

| 参数      | 描述                                 |
| --------- | ------------------------------------ |
| markers:  | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

| 参数            | 描述                                |
| --------------- | ----------------------------------- |
| target_container: | 要将元素移动到的容器（默认：父容器）     |
| target_index:   | 目标 slot 内的索引（默认：追加到末尾）   |
| target_slot:   | 目标容器内的 slot（默认：默认 slot） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

| 参数            | 描述                                                                                                  |
| --------------- | --------------------------------------------------------------------------------------------------- |
| type:           | 事件的名称（例如，“click”、“mousedown”或“update:model-value”）                                                  |
| handler:        | 事件发生时调用的回调                                                                                      |
| args:           |  包含在发送到事件处理程序的消息中的参数（默认：`None` 表示全部）                                                                     |
| throttle:       | 事件发生之间的最短时间（以秒为单位）（默认：0.0）                                                                        |
| leading_events:  | 是否在第一次事件发生时立即触发事件处理程序（默认：`True`）                                                                        |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认：`True`）                                                                        |
| js_handler:     |  JavaScript 代码，该代码在事件发生时执行，例如 `(evt) => alert(evt)`（默认：`None`） |

**`on_change`**`(callback: Union[Callable[[nicegui.events.JsonEditorChangeEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个回调函数，以便在内容更改时调用。

**`on_select`**`(callback: Union[Callable[[nicegui.events.JsonEditorSelectEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个回调函数，以便在选中某些内容时调用。

**`remove`**`(element: Union[Element, int]) -> None`

删除一个子元素。

| 参数      | 描述                         |
| --------- | ---------------------------- |
| element:  | 元素实例或其 ID |

**`run_editor_method`**`(name: str, *args, timeout: float = 1) -> nicegui.awaitable_response.AwaitableResponse`

运行 JSONEditor 实例的方法。

有关方法的列表，请参见 [JSONEditor README](https://github.com/josdejong/svelte-jsoneditor/)。

如果等待该函数，则会返回方法调用的结果。
否则，该方法将执行而不等待响应。

| 参数      | 描述                                                                |
| --------- | ------------------------------------------------------------------- |
| name:     | 方法的名称（前缀“:”表示参数是 JavaScript 表达式）                                                               |
| args:     | 传递给方法的参数（Python 对象或 JavaScript 表达式）                         |
| timeout:  | 超时时间（以秒为单位）（默认：1 秒）                                                               |
| return:   | 可以等待以获取方法调用结果的 AwaitableResponse     |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果等待该函数，则会返回方法调用的结果。
否则，该方法将执行而不等待响应。

| 参数      | 描述                               |
| --------- | ---------------------------------- |
| name:     | 方法的名称                           |
| args:     | 传递给方法的参数                       |
| timeout:  | 等待响应的最大时间（默认：1 秒） |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

| 参数      | 描述                               |
| --------- | ---------------------------------- |
| visible:  | 元素是否应可见。                   |

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

| 参数      | 描述             |
| --------- | ---------------- |
| text:     | 工具提示的文本 |

**`update`**`() -> None`

### Inheritance

*   `Element`
*   `Visibility`
