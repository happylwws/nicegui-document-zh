# ui.aggrid

## AG Grid

这是一个使用 AG Grid创建网格的元素。

方法 `run_grid_method` 和 `run_row_method` 可用于与客户端的 AG Grid 实例进行交互。

| 选项 | 描述 |
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

## Select AG Grid Rows

您可以向网格单元格添加复选框，以允许用户选择单个或多个行。

要检索当前选定的行，请使用 `get_selected_rows` 方法。
此方法返回一个包含行字典的列表。

如果 `rowSelection` 设置为 `'single'` 或要获取第一个选定的行，您还可以使用 `get_selected_row` 方法。
此方法返回单个行作为字典；如果没有选择行，则返回 `None`。

有关更多信息，请参阅 [AG Grid 文档](https://www.ag-grid.com/javascript-data-grid/row-selection/#example-single-row-selection)。

```python
from nicegui import ui

grid = ui.aggrid({
    'columnDefs': [
        {'headerName': 'Name', 'field': 'name', 'checkboxSelection': True},
        {'headerName': 'Age', 'field': 'age'},
    ],
    'rowData': [
        {'name': 'Alice', 'age': 18},
        {'name': 'Bob', 'age': 21},
        {'name': 'Carol', 'age': 42},
    ],
    'rowSelection': 'multiple',
}).classes('max-h-40')

async def output_selected_rows():
    rows = await grid.get_selected_rows()
    if rows:
        for row in rows:
            ui.notify(f"{row['name']}, {row['age']}")
    else:
        ui.notify('No rows selected.')

async def output_selected_row():
    row = await grid.get_selected_row()
    if row:
        ui.notify(f"{row['name']}, {row['age']}")
    else:
        ui.notify('No row selected!')

ui.button('Output selected rows', on_click=output_selected_rows)
ui.button('Output selected row', on_click=output_selected_row)

ui.run()
```

## Filter Rows using Mini Filters

您可以向每列的标题添加[迷你过滤器](https://ag-grid.com/javascript-data-grid/filter-set-mini-filter/)来过滤行。

请注意，“agTextColumnFilter”如何匹配单个字符，例如“Alice”和“Carol”中的“a”，而“agNumberColumnFilter”如何匹配整个数字，例如“18”和“21”，但不匹配“1”。

```python
from nicegui import ui

ui.aggrid({
    'columnDefs': [
        {'headerName': 'Name', 'field': 'name', 'filter': 'agTextColumnFilter', 'floatingFilter': True},
        {'headerName': 'Age', 'field': 'age', 'filter': 'agNumberColumnFilter', 'floatingFilter': True},
    ],
    'rowData': [
        {'name': 'Alice', 'age': 18},
        {'name': 'Bob', 'age': 21},
        {'name': 'Carol', 'age': 42},
    ],
}).classes('max-h-40')

ui.run()
```

## AG Grid with Conditional Cell Formatting

此演示展示了如何使用 `cellClassRules` 根据单元格的值有条件地格式化单元格。

```python
from nicegui import ui

ui.aggrid({
    'columnDefs': [
        {'headerName': 'Name', 'field': 'name'},
        {'headerName': 'Age', 'field': 'age', 'cellClassRules': {
            'bg-red-300': 'x < 21',
            'bg-green-300': 'x >= 21',
        }},
    ],
    'rowData': [
        {'name': 'Alice', 'age': 18},
        {'name': 'Bob', 'age': 21},
        {'name': 'Carol', 'age': 42},
    ],
})

ui.run()
```

## Create Grid from Pandas DataFrame

您可以使用 `from_pandas` 方法从 Pandas DataFrame 创建 AG Grid。
此方法将 Pandas DataFrame 作为输入并返回 AG Grid。

```python
import pandas as pd
from nicegui import ui

df = pd.DataFrame(data={'col1': [1, 2], 'col2': [3, 4]})
ui.aggrid.from_pandas(df).classes('max-h-40')

ui.run()
```

## Create Grid from Polars DataFrame

您可以使用 `from_polars` 方法从 Polars DataFrame 创建 AG Grid。
此方法将 Polars DataFrame 作为输入并返回 AG Grid。

```python
import polars as pl
from nicegui import ui

df = pl.DataFrame(data={'col1': [1, 2], 'col2': [3, 4]})
ui.aggrid.from_polars(df).classes('max-h-40')

ui.run()
```

## Render columns as HTML

您可以通过将列索引列表传递给 `html_columns` 参数来将列呈现为 HTML。

```python
from nicegui import ui

ui.aggrid({
    'columnDefs': [
        {'headerName': 'Name', 'field': 'name'},
        {'headerName': 'URL', 'field': 'url'},
    ],
    'rowData': [
        {'name': 'Google', 'url': '<a href="https://google.com">https://google.com</a>'},
        {'name': 'Facebook', 'url': '<a href="https://facebook.com">https://facebook.com</a>'},
    ],
}, html_columns=[1])

ui.run()
```

## Respond to an AG Grid event

所有 AG Grid 事件都通过 AG Grid 全局监听器传递到 NiceGUI。
可以使用 `.on()` 方法订阅这些事件。

```python
from nicegui import ui

ui.aggrid({
    'columnDefs': [
        {'headerName': 'Name', 'field': 'name'},
        {'headerName': 'Age', 'field': 'age'},
    ],
    'rowData': [
        {'name': 'Alice', 'age': 18},
        {'name': 'Bob', 'age': 21},
        {'name': 'Carol', 'age': 42},
    ],
}).on('cellClicked', lambda event: ui.notify(f'Cell value: {event.args["value"]}'))

ui.run()
```

## AG Grid with complex objects

您可以通过使用句点分隔字段名称，在 AG Grid 中使用嵌套的复杂对象。
（这就是为什么不允许 `rowData` 中的键包含句点的原因。）

```python
from nicegui import ui

ui.aggrid({
    'columnDefs': [
        {'headerName': 'First name', 'field': 'name.first'},
        {'headerName': 'Last name', 'field': 'name.last'},
        {'headerName': 'Age', 'field': 'age'}
    ],
    'rowData': [
        {'name': {'first': 'Alice', 'last': 'Adams'}, 'age': 18},
        {'name': {'first': 'Bob', 'last': 'Brown'}, 'age': 21},
        {'name': {'first': 'Carol', 'last': 'Clark'}, 'age': 42},
    ],
}).classes('max-h-40')

ui.run()
```

## AG Grid with dynamic row height

您可以通过将函数传递给 `getRowHeight` 参数来设置各个行的高度。

```python
from nicegui import ui

ui.aggrid({
    'columnDefs': [{'field': 'name'}, {'field': 'age'}],
    'rowData': [
        {'name': 'Alice', 'age': '18'},
        {'name': 'Bob', 'age': '21'},
        {'name': 'Carol', 'age': '42'},
    ],
    ':getRowHeight': 'params => params.data.age > 35 ? 50 : 25',
}).classes('max-h-40')

ui.run()
```

## Run row methods

您可以使用 `run_row_method` 方法在单个行上运行方法。
此方法将行 ID、方法名称和方法参数作为参数。
行 ID 是行索引（作为字符串）或 `getRowId` 函数的值。

以下演示展示了如何使用它来更新单元格值。
请注意，当值更新时，会保留行选择。
如果使用 `update` 方法更新网格，则不会出现这种情况。

```python
from nicegui import ui

grid = ui.aggrid({
    'columnDefs': [
        {'field': 'name', 'checkboxSelection': True},
        {'field': 'age'},
    ],
    'rowData': [
        {'name': 'Alice', 'age': 18},
        {'name': 'Bob', 'age': 21},
        {'name': 'Carol', 'age': 42},
    ],
    ':getRowId': '(params) => params.data.name',
})
ui.button('Update',
          on_click=lambda: grid.run_row_method('Alice', 'setDataValue', 'age', 99))

ui.run()
```

## Filter return values

您可以通过传递定义 JavaScript 函数的字符串来过滤方法调用的返回值。
此演示运行网格方法 “getDisplayedRowAtIndex”，并返回结果的 “data” 属性。

请注意，仅页面函数支持从客户端请求数据，而不支持共享的自动索引页面。

```python
from nicegui import ui

@ui.page('/')
def page():
    grid = ui.aggrid({
        'columnDefs': [{'field': 'name'}],
        'rowData': [{'name': 'Alice'}, {'name': 'Bob'}],
    })

    async def get_first_name() -> None:
        row = await grid.run_grid_method('g => g.getDisplayedRowAtIndex(0).data')
        ui.notify(row['name'])

    ui.button('Get First Name', on_click=get_first_name)

ui.run()
```

## Handle theme change

您可以通过添加或删除类来更改 AG Grid 的主题。
此演示展示了如何使用开关来更改主题。

```python
from nicegui import events, ui

grid = ui.aggrid({})

def handle_theme_change(e: events.ValueChangeEventArguments):
    grid.classes(add='ag-theme-balham-dark' if e.value else 'ag-theme-balham',
                 remove='ag-theme-balham ag-theme-balham-dark')

ui.switch('Dark', on_change=handle_theme_change)

ui.run()
```

## Reference

### Initializer

| 选项 | 描述 |
| --- | --- |
| options: | AG Grid 选项的字典 |
| html_columns: | 应该作为 HTML 渲染的列列表（默认: `[]`） |
| theme: | AG Grid 主题（默认: 'balham'） |
| auto_size_columns: | 是否自动调整列宽以适应网格宽度（默认: `True`） |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`is_deleted`**`: 'bool'`

元素是否已被删除。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`options`**`: Dict`

选项字典。

**`props`**`: 'Props[Self]'`

元素的 props。

**`style`**`: 'Style[Self]'`

元素的样式。

**`visible`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

将资源添加到元素。

| 参数 | 描述 |
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

| 参数 | 描述 |
| --- | --- |
| name: | slot 的名称 |
| template: | slot 的 Vue 模板 |
| return: | slot |

**`ancestors`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的祖先。

| 参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并且在值更改时发生。
反向绑定优先用于初始同步。

| 参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性从目标对象的 target_name 属性绑定。

绑定是单向的，仅从目标到该元素。
更新会立即发生，并且在值更改时发生。

| 参数 | 描述 |
| --- | --- |
| target_object: | 要绑定自的对象。 |
| target_name: | 要绑定自的属性的名称。 |
| backward: | 在将其应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并且在值更改时发生。

| 参数 | 描述 |
| --- | --- |
| target_object: | 要绑定到的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将其应用于目标之前应用于该值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用[Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类来修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类会很有帮助。
该类的所有元素都将共享这些 HTML 类。
这些必须在元素实例化之前定义。

| 参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的类字符串 |
| remove: | 要从元素中删除的以空格分隔的类字符串 |
| toggle: | 要切换的以空格分隔的类字符串 |
| replace: | 要使用的代替现有类的以空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或其布局。
由于属性只是作为 HTML 属性应用，它们可以与任何 HTML 元素一起使用。
该类的所有元素都将共享这些属性。
这些必须在元素实例化之前定义。

如果未指定值，则假定布尔属性为 `True`。

| 参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的布尔值或要添加的键=值对的列表 |
| remove: | 要删除的属性键的以空格分隔的列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式会很有帮助。
该类的所有元素都将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

| 参数 | 描述 |
| --- | --- |
| add: | 要添加到元素的以分号分隔的样式列表 |
| remove: | 要从元素中删除的以分号分隔的样式列表 |
| replace: | 要使用的代替现有样式的以分号分隔的样式列表 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

| 参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

`@classmethod`
**`from_pandas`**`(df: pd.DataFrame, theme: str = 'balham', auto_size_columns: bool = True, options: Dict = {}) -> Self`

从 Pandas DataFrame 创建 AG Grid。

注意：
如果 DataFrame 包含类型为 `datetime64[ns]`，`timedelta64[ns]`，`complex128` 或 `period[M]` 的不可序列化列，它们将被转换为字符串。
要使用不同的转换，请在将 DataFrame 传递给此方法之前手动转换它。
有关更多信息，请参见 [issue 1698](https://github.com/zauberzeug/nicegui/issues/1698)。

| 参数 | 描述 |
| --- | --- |
| df: | Pandas DataFrame |
| theme: | AG Grid 主题 (默认: 'balham') |
| auto_size_columns: | 是否自动调整列宽以适应网格宽度（默认: `True`） |
| options: | 其他 AG Grid 选项的字典 |
| return: | AG Grid 元素 |

`@classmethod`
**`from_polars`**`(df: pl.DataFrame, theme: str = 'balham', auto_size_columns: bool = True, options: Dict = {}) -> Self`

从 Polars DataFrame 创建 AG Grid。

如果 DataFrame 包含非 UTF-8 数据类型，它们将被转换为字符串。
要使用不同的转换，请在将 DataFrame 传递给此方法之前手动转换它。

| 参数 | 描述 |
| --- | --- |
| df: | Polars DataFrame |
| theme: | AG Grid 主题 (默认: 'balham') |
| auto_size_columns: | 是否自动调整列宽以适应网格宽度（默认: `True`） |
| options: | 其他 AG Grid 选项的字典 |
| return: | AG Grid 元素 |

**`get_client_data`**`(timeout: float = 1, method: Literal['all_unsorted', 'filtered_unsorted', 'filtered_sorted', 'leaf'] = 'all_unsorted') -> List[Dict]`

从客户端获取数据，包括客户端所做的任何编辑。

当网格配置为 `editable: True` 时，此方法特别有用。

有关更多信息，请参见 [AG Grid API](https://www.ag-grid.com/javascript-data-grid/accessing-data/)。

请注意，当编辑单元格时，只有当单元格退出编辑模式时，行数据才会更新。
除非设置了 `stopEditingWhenCellsLoseFocus: True`，否则当单元格失去焦点时，不会发生这种情况。

| 参数 | 描述 |
| --- | --- |
| timeout: | 超时时间（秒）（默认值：1 秒） |
| method: | 访问数据的方法，"all_unsorted" (默认), "filtered_unsorted", "filtered_sorted", "leaf" |
| return: | 行数据的列表 |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

应等待此函数，以便正确返回计算属性。

| 参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`get_selected_row`**`() -> Optional[Dict]`

获取当前选择的单个行。

当网格配置为 `rowSelection: 'single'` 时，此方法特别有用。

| 参数 | 描述 |
| --- | --- |
| return: | 如果选择了任何行，则返回第一个选定的行的行数据；否则返回 `None`。 |

**`get_selected_rows`**`() -> List[Dict]`

获取当前选择的行。

当网格配置为 `rowSelection: 'multiple'` 时，此方法特别有用。

有关更多信息，请参见 [AG Grid API](https://www.ag-grid.com/javascript-data-grid/row-selection/#reference-selection-getSelectedRows)。

| 参数 | 描述 |
| --- | --- |
| return: | 选定的行数据的列表 |

**`load_client_data`**`() -> None`

获取客户端数据，并使用它更新元素的行数据。

这会将客户端在可编辑单元格中所做的编辑同步到服务器。

请注意，当编辑单元格时，只有当单元格退出编辑模式时，行数据才会更新。
除非设置了 `stopEditingWhenCellsLoseFocus: True`，否则当单元格失去焦点时，不会发生这种情况。

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于识别元素，以便使用 [ElementFilter](documentation/element_filter) 进行查询，
该过滤器在测试中大量使用，但也可用于减少全局变量的数量或传递依赖项。

| 参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或包含以空格分隔的标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

| 参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器 (默认: 父容器) |
| target_index: | 目标 slot 内的索引 (默认: 追加到末尾) |
| target_slot: | 目标容器内的 slot (默认: 默认 slot) |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

| 参数 | 描述 |
| --- | --- |
| type: | 事件名称 (例如 "click", "mousedown", 或 "update:model-value") |
| handler: | 事件发生时调用的回调 |
| args: | 事件消息中包含的参数发送到事件处理程序 (默认: `None` 表示所有) |
| throttle: | 事件发生之间的最小时间（以秒为单位）（默认值：0.0） |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`） |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`） |
| js_handler: | 在事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)` (默认: `None`) |

**`remove`**`(element: Union[Element, int]) -> None`

删除一个子元素。

| 参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`run_column_method`**`(name: str, *args, timeout: float = 1) -> nicegui.awaitable_response.AwaitableResponse`

此方法已弃用。请改用 `run_grid_method`。

有关更多信息，请参见[https://www.ag-grid.com/javascript-data-grid/column-api/](https://www.ag-grid.com/javascript-data-grid/column-api/)。

**`run_grid_method`**`(name: str, *args, timeout: float = 1) -> nicegui.awaitable_response.AwaitableResponse`

运行 AG Grid API 方法。

有关方法列表，请参见 [AG Grid API](https://www.ag-grid.com/javascript-data-grid/grid-api/)。

如果等待函数，则返回方法调用的结果。
否则，将执行该方法而不等待响应。

| 参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 要传递给方法的参数 |
| timeout: | 超时时间（秒）（默认值：1 秒） |
| return: | 可以等待以获取方法调用结果的 AwaitableResponse |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行方法。

如果等待函数，则返回方法调用的结果。
否则，将执行该方法而不等待响应。

| 参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 要传递给方法的参数 |
| timeout: | 等待响应的最大时间（默认值：1 秒） |

**`run_row_method`**`(row_id: str, name: str, *args, timeout: float = 1) -> nicegui.awaitable_response.AwaitableResponse`

在特定行上运行 AG Grid API 方法。

有关方法列表，请参见 [AG Grid Row Reference](https://www.ag-grid.com/javascript-data-grid/row-object/)。

如果等待函数，则返回方法调用的结果。
否则，将执行该方法而不等待响应。

| 参数 | 描述 |
| --- | --- |
| row_id: | 行的 ID（由 `getRowId` 选项定义） |
| name: | 方法的名称 |
| args: | 要传递给方法的参数 |
| timeout: | 超时时间（秒）（默认值：1 秒） |
| return: | 可以等待以获取方法调用结果的 AwaitableResponse |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

| 参数 | 描述 |
| --- | --- |
| visible: | 元素是否应该可见。 |

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

| 参数 | 描述 |
| --- | --- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

### Inheritance

* `Element`
* `Visibility`
