# ui.table

## Table

一个基于 Quasar 的 [QTable](https://quasar.dev/vue-components/table) 组件的表格。

| 选项 | 描述 |
| --- | --- |
| rows: | 行对象的列表 |
| columns: | 列对象的列表 (默认为第一行的列) |
| column_defaults: | 可选的默认列属性 |
| row_key: | 包含唯一标识行的列的名称 (默认: "id") |
| title: | 表格的标题 |
| selection: | 选择类型 ("single" 或 "multiple"; 默认: `None`) |
| pagination: | 与分页对象或每页行数相关的字典 (`None` 隐藏分页，0 表示 "无限"; 默认: `None`)。 |
| on_select: | 当选择发生变化时调用的回调函数 |
| on_pagination_change: | 当分页发生变化时调用的回调函数 |

如果选择类型是 'single' 或 'multiple'，则可以使用 `selected` 属性访问选定的行。

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

## Omitting columns

如果你省略 `columns` 参数，表格将自动从第一行生成列。标签将大写，并启用排序。

```python
from nicegui import ui

ui.table(rows=[
    {'make': 'Toyota', 'model': 'Celica', 'price': 35000},
    {'make': 'Ford', 'model': 'Mondeo', 'price': 32000},
    {'make': 'Porsche', 'model': 'Boxster', 'price': 72000},
])

ui.run()
```

## Default column parameters

你可以定义应用于所有列的默认列参数。在本例中，所有列默认左对齐，并具有蓝色大写标题。

```python
from nicegui import ui

ui.table(rows=[
    {'name': 'Alice', 'age': 18},
    {'name': 'Bob', 'age': 21},
], columns=[
    {'name': 'name', 'label': 'Name', 'field': 'name'},
    {'name': 'age', 'label': 'Age', 'field': 'age'},
], column_defaults={
    'align': 'left',
    'headerClasses': 'uppercase text-primary',
})

ui.run()
```

## Table with expandable rows

可以使用作用域插槽来插入切换表格行展开状态的按钮。有关更多信息，请参阅 [Quasar 文档](https://quasar.dev/vue-components/table#expanding-rows)。

```python
from nicegui import ui

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name'},
    {'name': 'age', 'label': 'Age', 'field': 'age'},
]
rows = [
    {'name': 'Alice', 'age': 18},
    {'name': 'Bob', 'age': 21},
    {'name': 'Carol'},
]

table = ui.table(columns=columns, rows=rows, row_key='name').classes('w-72')
table.add_slot('header', r'''
    <q-tr :props="props">
        <q-th auto-width />
        <q-th v-for="col in props.cols" :key="col.name" :props="props">
            {{ col.label }}
        </q-th>
    </q-tr>
''')
table.add_slot('body', r'''
    <q-tr :props="props">
        <q-td auto-width>
            <q-btn size="sm" color="accent" round dense
                @click="props.expand = !props.expand"
                :icon="props.expand ? 'remove' : 'add'" />
        </q-td>
        <q-td v-for="col in props.cols" :key="col.name" :props="props">
            {{ col.value }}
        </q-td>
    </q-tr>
    <q-tr v-show="props.expand" :props="props">
        <q-td colspan="100%">
            <div class="text-left">This is {{ props.row.name }}.</div>
        </q-td>
    </q-tr>
''')

ui.run()
```

## Show and hide columns

这是一个如何在表格中显示和隐藏列的示例。

```python
from nicegui import ui
from typing import Dict

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name', 'required': True, 'align': 'left'},
    {'name': 'age', 'label': 'Age', 'field': 'age', 'sortable': True},
]
rows = [
    {'name': 'Alice', 'age': 18},
    {'name': 'Bob', 'age': 21},
    {'name': 'Carol'},
]
table = ui.table(columns=columns, rows=rows, row_key='name')

def toggle(column: Dict, visible: bool) -> None:
    column['classes'] = '' if visible else 'hidden'
    column['headerClasses'] = '' if visible else 'hidden'
    table.update()

with ui.button(icon='menu'):
    with ui.menu(), ui.column().classes('gap-0 p-2'):
        for column in columns:
            ui.switch(column['label'], value=True, on_change=lambda e,
                      column=column: toggle(column, e.value))

ui.run()
```

## Table with drop down selection

这是一个如何在表格中使用下拉选择的示例。在从作用域插槽发出 `rename` 事件后，`rename` 函数会更新表格行。

```python
from nicegui import events, ui

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name'},
    {'name': 'age', 'label': 'Age', 'field': 'age'},
]
rows = [
    {'id': 0, 'name': 'Alice', 'age': 18},
    {'id': 1, 'name': 'Bob', 'age': 21},
    {'id': 2, 'name': 'Carol'},
]
name_options = ['Alice', 'Bob', 'Carol']

def rename(e: events.GenericEventArguments) -> None:
    for row in rows:
        if row['id'] == e.args['id']:
            row['name'] = e.args['name']
    ui.notify(f'Table.rows is now: {table.rows}')

table = ui.table(columns=columns, rows=rows).classes('w-full')
table.add_slot('body-cell-name', r'''
    <q-td key="name" :props="props">
        <q-select
            v-model="props.row.name"
            :options="''' + str(name_options) + r'''"
            @update:model-value="() => $parent.$emit('rename', props.row)"
        />
    </q-td>
''')
table.on('rename', rename)

ui.run()
```

## Table from Pandas DataFrame

您可以使用 `from_pandas` 方法从 Pandas DataFrame 创建表格。此方法接受 Pandas DataFrame 作为输入并返回一个表格。

```python
import pandas as pd
from nicegui import ui

df = pd.DataFrame(data={'col1': [1, 2], 'col2': [3, 4]})
ui.table.from_pandas(df).classes('max-h-40')

ui.run()
```

## Table from Polars DataFrame

您可以使用 `from_polars` 方法从 Polars DataFrame 创建表格。此方法接受 Polars DataFrame 作为输入并返回一个表格。

```python
import polars as pl
from nicegui import ui

df = pl.DataFrame(data={'col1': [1, 2], 'col2': [3, 4]})
ui.table.from_polars(df).classes('max-h-40')

ui.run()
```

## Adding rows

使用 `add_row(dict)` 和 `add_rows(list[dict])` 方法添加新行很简单。设置 "virtual-scroll" 属性后，可以使用 `scrollTo` JavaScript 函数以编程方式滚动表格。

```python
from datetime import datetime
from nicegui import ui

def add():
    table.add_row({'date': datetime.now().strftime('%c')})
    table.run_method('scrollTo', len(table.rows)-1)

columns = [{'name': 'date', 'label': 'Date', 'field': 'date'}]
table = ui.table(columns=columns, rows=[]).classes('h-52').props('virtual-scroll')
ui.button('Add row', on_click=add)

ui.run()
```

## Custom sorting and formatting

您可以使用 `:` 前缀定义动态列属性。这样可以定义自定义排序和格式化函数。

以下示例允许按长度对 `name` 列进行排序。`age` 列的格式设置为显示年龄（以年为单位）。

```python
from nicegui import ui

columns = [
    {
        'name': 'name',
        'label': 'Name',
        'field': 'name',
        'sortable': True,
        ':sort': '(a, b, rowA, rowB) => b.length - a.length',
    },
    {
        'name': 'age',
        'label': 'Age',
        'field': 'age',
        ':format': 'value => value + " years"',
    },
]
rows = [
    {'name': 'Alice', 'age': 18},
    {'name': 'Bob', 'age': 21},
    {'name': 'Carl', 'age': 42},
]
ui.table(columns=columns, rows=rows, row_key='name')

ui.run()
```

## Toggle fullscreen

您可以使用 `toggle_fullscreen()` 方法切换表格的全屏模式。

```python
from nicegui import ui

table = ui.table(
    columns=[{'name': 'name', 'label': 'Name', 'field': 'name'}],
    rows=[{'name': 'Alice'}, {'name': 'Bob'}, {'name': 'Carol'}],
).classes('w-full')

with table.add_slot('top-left'):
    def toggle() -> None:
        table.toggle_fullscreen()
        button.props('icon=fullscreen_exit' if table.is_fullscreen else 'icon=fullscreen')
    button = ui.button('Toggle fullscreen', icon='fullscreen', on_click=toggle).props('flat')

ui.run()
```

## Pagination

您可以提供单个整数或字典来定义分页。

字典可以包含以下键：

*   `rowsPerPage`: 每页的行数。
*   `sortBy`: 要排序的列名。
*   `descending`: 是否以降序排序。
*   `page`: 当前页（从 1 开始）。

```python
from nicegui import ui

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name', 'required': True, 'align': 'left'},
    {'name': 'age', 'label': 'Age', 'field': 'age', 'sortable': True},
]
rows = [
    {'name': 'Elsa', 'age': 18},
    {'name': 'Oaken', 'age': 46},
    {'name': 'Hans', 'age': 20},
    {'name': 'Sven'},
    {'name': 'Olaf', 'age': 4},
    {'name': 'Anna', 'age': 17},
]
ui.table(columns=columns, rows=rows, pagination=3)
ui.table(columns=columns, rows=rows, pagination={'rowsPerPage': 4, 'sortBy': 'age', 'page': 2})

ui.run()
```

## Handle pagination changes

您可以使用 `on_pagination_change` 参数处理分页更改。

```python
from nicegui import ui

ui.table(
    columns=[{'id': 'Name', 'label': 'Name', 'field': 'Name', 'align': 'left'}],
    rows=[{'Name': f'Person {i}'} for i in range(100)],
    pagination=3,
    on_pagination_change=lambda e: ui.notify(e.value),
)

ui.run()
```

## Computed props

您可以在异步回调函数中访问表格的计算属性。

```python
from nicegui import ui

async def show_filtered_sorted_rows():
    ui.notify(await table.get_filtered_sorted_rows())

async def show_computed_rows():
    ui.notify(await table.get_computed_rows())

table = ui.table(
    columns=[
        {'name': 'name', 'label': 'Name', 'field': 'name', 'align': 'left', 'sortable': True},
        {'name': 'age', 'label': 'Age', 'field': 'age', 'align': 'left', 'sortable': True}
    ],
    rows=[
        {'name': 'Noah', 'age': 33},
        {'name': 'Emma', 'age': 21},
        {'name': 'Rose', 'age': 88},
        {'name': 'James', 'age': 59},
        {'name': 'Olivia', 'age': 62},
        {'name': 'Liam', 'age': 18},
    ],
    row_key='name',
    pagination=3,
)
ui.input('Search by name/age').bind_value(table, 'filter')
ui.button('Show filtered/sorted rows', on_click=show_filtered_sorted_rows)
ui.button('Show computed rows', on_click=show_computed_rows)

ui.run()
```

## Computed fields

您可以使用函数来计算列的值。该函数接收行作为参数。有关更多信息，请参阅 [Quasar 文档](https://quasar.dev/vue-components/table#defining-the-columns)。

```python
from nicegui import ui

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name', 'align': 'left'},
    {'name': 'length', 'label': 'Length', ':field': 'row => row.name.length'},
]
rows = [
    {'name': 'Alice'},
    {'name': 'Bob'},
    {'name': 'Christopher'},
]
ui.table(columns=columns, rows=rows, row_key='name')

ui.run()
```

## Conditional formatting

您可以使用作用域插槽来有条件地格式化单元格的内容。有关 body-cell 插槽的更多信息，请参阅 [Quasar 文档](https://quasar.dev/vue-components/table#example--body-cell-slot)。

在此演示中，如果此人未满 21 岁，我们将使用 `q-badge` 以红色显示年龄。我们使用 `body-cell-age` 插槽将 `q-badge` 插入 `age` 列中。如果年龄小于 21 岁，则 `q-badge` 的 ":color" 属性设置为 "red"，否则设置为 "green"。":" 在 "color" 属性之前表示该值是一个 JavaScript 表达式。

```python
from nicegui import ui

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name'},
    {'name': 'age', 'label': 'Age', 'field': 'age'},
]
rows = [
    {'name': 'Alice', 'age': 18},
    {'name': 'Bob', 'age': 21},
    {'name': 'Carol', 'age': 42},
]
table = ui.table(columns=columns, rows=rows, row_key='name')
table.add_slot('body-cell-age', '''
    <q-td key="age" :props="props">
        <q-badge :color="props.value < 21 ? 'red' : 'green'">
            {{ props.value }}
        </q-badge>
    </q-td>
''')

ui.run()
```

## Table cells with links

这是一个演示如何在表格单元格中插入链接的示例。我们使用 `body-cell-link` 插槽在 `link` 列中插入一个 `<a>` 标签。

```python
from nicegui import ui

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name', 'align': 'left'},
    {'name': 'link', 'label': 'Link', 'field': 'link', 'align': 'left'},
]
rows = [
    {'name': 'Google', 'link': 'https://google.com'},
    {'name': 'Facebook', 'link': 'https://facebook.com'},
    {'name': 'Twitter', 'link': 'https://twitter.com'},
]
table = ui.table(columns=columns, rows=rows, row_key='name')
table.add_slot('body-cell-link', '''
    <q-td :props="props">
        <a :href="props.value">{{ props.value }}</a>
    </q-td>
''')

ui.run()
```

## Table with masonry-like grid

您可以使用 `grid` 属性将表格显示为类似砌体的网格。 有关更多信息，请参阅 [Quasar 文档](https://quasar.dev/vue-components/table#grid-style)。

```python
from nicegui import ui

columns = [
    {'name': 'name', 'label': 'Name', 'field': 'name'},
    {'name': 'age', 'label': 'Age', 'field': 'age'},
]
rows = [
    {'name': 'Alice', 'age': 18},
    {'name': 'Bob', 'age': 21},
    {'name': 'Carol', 'age': 42},
]
table = ui.table(columns=columns, rows=rows, row_key='name').props('grid')
table.add_slot('item', r'''
    <q-card flat bordered :props="props" class="m-1">
        <q-card-section class="text-center">
            <strong>{{ props.row.name }}</strong>
        </q-card-section>
        <q-separator />
        <q-card-section class="text-center">
            <div>{{ props.row.age }} years</div>
        </q-card-section>
    </q-card>
''')

ui.run()
```

## Reference

### Initializer

| 参数 | 描述 |
| --- | --- |
| rows: | 行对象的列表 |
| columns: | 列对象的列表 (默认为第一行的列) |
| column_defaults: | 可选的默认列属性 |
| row_key: | 包含唯一标识行的列的名称 (默认: "id") |
| title: | 表格的标题 |
| selection: | 选择类型 ("single" 或 "multiple"; 默认: `None`) |
| pagination: | 与分页对象或每页行数相关的字典 (`None` 隐藏分页，0 表示 "无限"; 默认: `None`)。 |
| on_select: | 当选择发生变化时调用的回调函数 |
| on_pagination_change: | 当分页发生变化时调用的回调函数 |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`column_defaults`**`: Optional[Dict] (settable)`

默认列属性。

**`columns`**`: List[Dict] (settable)`

列的列表。

**`filter`**`: BindableProperty`

**`is_deleted`**`: 'bool'`

元素是否已被删除。

**`is_fullscreen`**`: bool (settable)`

表格是否处于全屏模式。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`pagination`**`: dict (settable)`

分页对象。

**`props`**`: 'Props[Self]'`

元素的 props。

**`row_key`**`: str (settable)`

包含唯一标识行的列的名称。

**`rows`**`: List[Dict] (settable)`

行的列表。

**`selected`**`: List[Dict] (settable)`

选中行的列表。

**`style`**`: 'Style[Self]'`

元素的样式。

**`visible`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

将资源添加到元素。

|  参数 | 描述 |
| --- | --- |
| path: | 资源路径 (例如：包含 CSS 和 JavaScript 文件的文件夹) |

**`add_row`**`(row: Dict) -> None`

向表格添加一行。

**`add_rows`**`(rows: List[Dict], *args: Any) -> None`

向表格添加多行。

**`add_slot`**`(name: str, template: Optional[str] = None) -> Slot`

向元素添加一个slot。

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
| include_self: | 是否在迭代中包括元素本身 |

**`bind_filter`**`(target_object: Any, target_name: str = 'filter', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的过滤器绑定到目标对象的 target_name 属性。

绑定是双向的，从这个元素到目标，以及从目标到这个元素。
更新会立即发生，并且在值更改时发生。
后向绑定优先进行初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于值的函数。 |
| backward: | 在将值应用于此元素之前应用于值的函数。 |

**`bind_filter_from`**`(target_object: Any, target_name: str = 'filter', backward: Callable[..., Any] = [...]) -> Self`

从目标对象的 target_name 属性绑定此元素的过滤器。

绑定仅单向工作，从目标到此元素。
更新会立即发生，并且在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于值的函数。 |

**`bind_filter_to`**`(target_object: Any, target_name: str = 'filter', forward: Callable[..., Any] = [...]) -> Self`

将此元素的过滤器绑定到目标对象的 target_name 属性。

绑定仅单向工作，从该元素到目标。
更新会立即发生，并且在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于值的函数。 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从这个元素到目标，以及从目标到这个元素。
更新会立即发生，并且在值更改时发生。
后向绑定优先进行初始同步。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于值的函数。 |
| backward: | 在将值应用于此元素之前应用于值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此目标对象的 target_name 属性绑定此元素的可见性。

绑定仅单向工作，从目标到此元素。
更新会立即发生，并且在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要从中绑定的对象。 |
| target_name: | 要从中绑定的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅单向工作，从该元素到目标。
更新会立即发生，并且在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定到的属性的名称。 |
| forward: | 在将值应用于目标之前应用于值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类可能很有帮助。
此类的所有元素都将共享这些 HTML 类。
这些类必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的类字符串 |
| remove: | 以空格分隔的要从元素中删除的类字符串 |
| toggle: | 以空格分隔的要切换的类字符串 |
| replace: | 以空格分隔的类字符串，用于替换现有类 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或其布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素都将共享这些属性。
这些属性必须在元素实例化之前定义。

如果未指定值，则布尔属性假定为 `True`。

|  参数 | 描述 |
| --- | --- |
| add: | 以空格分隔的布尔值或键值对列表，用于添加 |
| remove: | 以空格分隔的要删除的属性键列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能很有帮助。
此类的所有元素都将共享这些 CSS 定义。
这些定义必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 以分号分隔的要添加到元素的样式列表 |
| remove: | 以分号分隔的要从元素中删除的样式列表 |
| replace: | 以分号分隔的样式列表，用于替换现有样式 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包括元素本身 |

`@classmethod`
**`from_pandas`**`(df: pd.DataFrame, columns: Optional[List[Dict]] = None, column_defaults: Optional[Dict] = None, row_key: str = 'id', title: Optional[str] = None, selection: Optional[Literal['single', 'multiple']] = None, pagination: Union[int, dict, NoneType] = None, on_select: Union[Callable[[nicegui.events.TableSelectionEventArguments], Any], Callable[[], Any], NoneType] = None) -> Self`

从 Pandas DataFrame 创建表格。

注意：
如果 DataFrame 包含类型为 `datetime64[ns]`、`timedelta64[ns]`、`complex128` 或 `period[M]` 的不可序列化列，
它们将被转换为字符串。
要使用不同的转换，请在将 DataFrame 传递给此方法之前手动转换它。
有关更多信息，请参阅 [issue 1698](https://github.com/zauberzeug/nicegui/issues/1698)。

|  参数 | 描述 |
| --- | --- |
| df: | Pandas DataFrame |
| columns: | 列对象的列表（默认为数据帧的列） |
| column_defaults: | 可选的默认列属性 |
| row_key: | 包含唯一标识行的列的名称（默认： "id"） |
| title: | 表格的标题 |
| selection: | 选择类型（"single" 或 "multiple"; 默认：`None`） |
| pagination: | 与分页对象或每页行数相关的字典（`None` 隐藏分页，0 表示 "无限"; 默认：`None`）。 |
| on_select: | 当选择更改时调用的回调函数 |
| return: | table element |

`@classmethod`
**`from_polars`**`(df: pl.DataFrame, columns: Optional[List[Dict]] = None, column_defaults: Optional[Dict] = None, row_key: str = 'id', title: Optional[str] = None, selection: Optional[Literal['single', 'multiple']] = None, pagination: Union[int, dict, NoneType] = None, on_select: Union[Callable[[nicegui.events.TableSelectionEventArguments], Any], Callable[[], Any], NoneType] = None) -> Self`

从 Polars DataFrame 创建表格。

注意：
如果 DataFrame 包含非 UTF-8 数据类型，它们将转换为字符串。
要使用不同的转换，请在将 DataFrame 传递给此方法之前手动转换它。

|  参数 | 描述 |
| --- | --- |
| df: | Polars DataFrame |
| columns: | 列对象的列表（默认为数据帧的列） |
| column_defaults: | 可选的默认列属性 |
| row_key: | 包含唯一标识行的列的名称（默认： "id"） |
| title: | 表格的标题 |
| selection: | 选择类型（"single" 或 "multiple"; 默认：`None`） |
| pagination: | 与分页对象或每页行数相关的字典（`None` 隐藏分页，0 表示 "无限"; 默认：`None`）。 |
| on_select: | 当选择更改时调用的回调函数 |
| return: | table element |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

应该等待此函数，以便正确返回计算属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最长时间（默认为 1 秒） |

**`get_computed_rows`**`(timeout: float = 1) -> List[Dict]`

异步返回表格的计算行。

**`get_computed_rows_number`**`(timeout: float = 1) -> int`

异步返回表格的计算行数。

**`get_filtered_sorted_rows`**`(timeout: float = 1) -> List[Dict]`

异步返回表格的筛选和排序行。

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于识别用于使用[ElementFilter](/documentation/element_filter) 查询的元素，这在测试中大量使用，
但也可以用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或用空格分隔的标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认为父容器） |
| target_index: | 目标 slot 中的索引（默认：附加到末尾） |
| target_slot: | 目标容器中的 slot（默认：默认 slot） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅一个事件。