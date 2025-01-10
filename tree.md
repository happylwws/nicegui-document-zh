# ui. *tree*

## Tree

使用 Quasar 的 [QTree](https://quasar.dev/vue-components/tree) 组件显示分层数据。

如果使用 ID，请确保它们在整个树中是唯一的。

要使用复选框和 `on_tick`，请将 `tick_strategy` 参数设置为 "leaf"，"leaf-filtered" 或 "strict"。

| 选项 | 描述 |
| --- | --- |
| nodes: | 节点对象的分层列表 |
| node_key: | 每个节点对象中保存其唯一 ID 的属性名称（默认: "id"） |
| label_key: | 每个节点对象中保存其标签的属性名称（默认: "label"） |
| children_key: | 每个节点对象中保存其子列表的属性名称（默认: "children"） |
| on_select: | 当节点选择更改时调用的回调函数 |
| on_expand: | 当节点展开状态更改时调用的回调函数 |
| on_tick: | 当节点被选中或取消选中时调用的回调函数 |
| tick_strategy: | 是否以及如何使用复选框（"leaf"，"leaf-filtered" 或 "strict"；默认: `None`） |

```python
from nicegui import ui

ui.tree([
    {'id': 'numbers', 'children': [{'id': '1'}, {'id': '2'}]},
    {'id': 'letters', 'children': [{'id': 'A'}, {'id': 'B'}]},
], label_key='id', on_select=lambda e: ui.notify(e.value))

ui.run()
```

## Tree with custom header and body

可以使用作用域插槽将自定义内容插入到树节点的头部和主体中。
有关更多信息，请参阅 [Quasar 文档](https://quasar.dev/vue-components/tree#customize-content)。

```python
from nicegui import ui

tree = ui.tree([
    {'id': 'numbers', 'description': 'Just some numbers', 'children': [
        {'id': '1', 'description': 'The first number'},
        {'id': '2', 'description': 'The second number'},
    ]},
    {'id': 'letters', 'description': 'Some latin letters', 'children': [
        {'id': 'A', 'description': 'The first letter'},
        {'id': 'B', 'description': 'The second letter'},
    ]},
], label_key='id', on_select=lambda e: ui.notify(e.value))

tree.add_slot('default-header', '''
    <span :props="props">Node <strong>{{ props.node.id }}</strong></span>
''')
tree.add_slot('default-body', '''
    <span :props="props">Description: "{{ props.node.description }}"</span>
''')

ui.run()
```

## Tree with checkboxes

可以通过设置 "tick-strategy" 属性将树与复选框一起使用。

```python
from nicegui import ui

ui.tree([
    {'id': 'A', 'children': [{'id': 'A1'}, {'id': 'A2'}]},
    {'id': 'B', 'children': [{'id': 'B1'}, {'id': 'B2'}]},
], label_key='id', tick_strategy='leaf', on_tick=lambda e: ui.notify(e.value))

ui.run()
```

## Expand/collapse programmatically

可以使用 `expand()` 和 `collapse()` 方法以编程方式切换整个树或单个节点。
即使节点被禁用（例如用户无法点击），这仍然有效。

```python
from nicegui import ui

t = ui.tree([
    {'id': 'A', 'children': [{'id': 'A1'}, {'id': 'A2'}], 'disabled': True},
    {'id': 'B', 'children': [{'id': 'B1'}, {'id': 'B2'}]},
], label_key='id').expand()

with ui.row():
    ui.button('+ all', on_click=t.expand)
    ui.button('- all', on_click=t.collapse)
    ui.button('+ A', on_click=lambda: t.expand(['A']))
    ui.button('- A', on_click=lambda: t.collapse(['A']))

ui.run()
```

## Select/deselect programmatically

您可以使用 `select()` 和 `deselect()` 方法选择或取消选择节点。

```python
from nicegui import ui

t = ui.tree([
    {'id': 'numbers', 'children': [{'id': '1'}, {'id': '2'}]},
    {'id': 'letters', 'children': [{'id': 'A'}, {'id': 'B'}]},
], label_key='id').expand()

with ui.row():
    ui.button('Select A', on_click=lambda: t.select('A'))
    ui.button('Deselect A', on_click=t.deselect)

ui.run()
```

## Tick/untick programmatically

设置 `tick_strategy` 后，您可以使用 `tick()` 和 `untick()` 方法勾选或取消勾选节点。
您可以指定节点键列表，也可以使用 `None` 来勾选或取消勾选所有节点。

```python
from nicegui import ui

t = ui.tree([
    {'id': 'numbers', 'children': [{'id': '1'}, {'id': '2'}]},
    {'id': 'letters', 'children': [{'id': 'A'}, {'id': 'B'}]},
], label_key='id', tick_strategy='leaf').expand()

with ui.row():
    ui.button('Tick 1, 2 and B', on_click=lambda: t.tick(['1', '2', 'B']))
    ui.button('Untick 2 and B', on_click=lambda: t.untick(['2', 'B']))
with ui.row():
    ui.button('Tick all', on_click=t.tick)
    ui.button('Untick all', on_click=t.untick)

ui.run()
```

## Filter nodes

您可以通过设置 `filter` 属性来过滤节点。
树将仅显示与过滤器匹配的节点。

```python
from nicegui import ui

t = ui.tree([
    {'id': 'fruits', 'children': [{'id': 'Apple'}, {'id': 'Banana'}]},
    {'id': 'vegetables', 'children': [{'id': 'Potato'}, {'id': 'Tomato'}]},
], label_key='id').expand()
ui.input('filter').bind_value_to(t, 'filter')

ui.run()
```

## Reference

### Initializer

|  |  |
| --- | --- |
| `nodes`: | 节点对象的分层列表 |
| `node_key`: | 每个节点对象中保存其唯一 ID 的属性名称（默认: "id"） |
| `label_key`: | 每个节点对象中保存其标签的属性名称（默认: "label"） |
| `children_key`: | 每个节点对象中保存其子列表的属性名称（默认: "children"） |
| `on_select`: | 当节点选择更改时调用的回调函数 |
| `on_expand`: | 当节点展开状态更改时调用的回调函数 |
| `on_tick`: | 当节点被选中或取消选中时调用的回调函数 |
| `tick_strategy`: | 是否以及如何使用复选框（"leaf"，"leaf-filtered" 或 "strict"；默认: `None`） |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`filter`**`: BindableProperty`

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
| include_self: | 是否在迭代中包含元素本身 |

**`bind_filter`**`(target_object: Any, target_name: str = 'filter', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的过滤器绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
后向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_filter_from`**`(target_object: Any, target_name: str = 'filter', backward: Callable[..., Any] = [...]) -> Self`

从此目标对象的 target_name 属性绑定此元素的过滤器。

绑定仅单向工作，从目标到此元素。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |

**`bind_filter_to`**`(target_object: Any, target_name: str = 'filter', forward: Callable[..., Any] = [...]) -> Self`

将此元素的过滤器绑定到目标对象的 target_name 属性。

绑定仅单向工作，从该元素到目标。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
后向绑定优先用于初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此目标对象的 target_name 属性绑定此元素的可见性。

绑定仅单向工作，从目标到此元素。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅单向工作，从该元素到目标。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`collapse`**`(node_keys: Optional[List[str]] = None) -> Self`

折叠给定节点。

|  参数 | 描述 |
| --- | --- |
| node_keys: | 要折叠的节点键列表（默认：所有节点） |

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
| replace: | 要使用的空格分隔的类字符串，而不是现有的类 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或其布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些属性。
这些必须在元素实例化之前定义。

如果没有指定值，则布尔属性假定为 `True`。

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
| include_self: | 是否在迭代中包含元素本身 |

**`deselect`**`() -> Self`

删除节点选择。

**`expand`**`(node_keys: Optional[List[str]] = None) -> Self`

展开给定节点。

|  参数 | 描述 |
| --- | --- |
| node_keys: | 要展开的节点键列表（默认：所有节点） |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回计算属性。

应等待此函数，以便正确返回计算的属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最长时间（默认值：1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于标识使用 [ElementFilter](/documentation/element_filter) 查询的元素，
该过滤器在测试中大量使用，
但也可用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认：父容器） |
| target_index: | 目标槽内的索引（默认：追加到末尾） |
| target_slot: | 目标容器内的槽（默认：默认槽） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如，“click”、“mousedown”或“update:model-value”） |
| handler: | 事件发生时调用的回调函数 |
| args: | 事件消息中包含的发送到事件处理程序的参数（默认值：`None`，表示全部） |
| throttle: | 事件发生之间的最短时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`on_expand`**`(callback: Union[Callable[[nicegui.events.ValueChangeEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个在展开状态更改时调用的回调。

**`on_select`**`(callback: Union[Callable[[nicegui.events.ValueChangeEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个在选择更改时调用的回调。

**`on_tick`**`(callback: Union[Callable[[nicegui.events.ValueChangeEventArguments], Any], Callable[[], Any]]) -> Self`

添加一个在节点被选中或取消选中时调用的回调。

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

**`select`**`(node_key: Optional[str]) -> Self`

选择给定的节点。

|  参数 | 描述 |
| --- | --- |
| node_key: | 要选择的节点键 |

**`set_filter`**`(filter_: str) -> None`

设置此元素的过滤器。

|  参数 | 描述 |
| --- | --- |
| filter: | 新的过滤器。 |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

|  参数 | 描述 |
| --- | --- |
| visible: | 元素是否应该可见。 |

**`tick`**`(node_keys: Optional[List[str]] = None) -> Self`

勾选给定的节点。

|  参数 | 描述 |
| --- | --- |
| node_keys: | 要勾选的节点键列表，或者使用 `None` 勾选所有节点（默认值：`None`） |

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

|  参数 | 描述 |
| --- | --- |
| text: | 工具提示的文本 |

**`untick`**`(node_keys: Optional[List[str]] = None) -> Self`

从给定节点中删除勾选。

|  参数 | 描述 |
| --- | --- |
| node_keys: | 要取消勾选的节点键列表，或者使用 `None` 取消勾选所有节点（默认值：`None`） |

**`update`**`() -> None`

在客户端更新元素。

### Inheritance

*   `FilterElement`
*   `Element`
*   `Visibility`
