# ui.restructured_text

## ReStructuredText

在页面上渲染 ReStructuredText。

| 参数    | 描述                     |
| :------ | :----------------------- |
| content | 要显示的 ReStructuredText 内容 |

```python
from nicegui import ui

ui.restructured_text('This is **reStructuredText**.')

ui.run()
```

## reStructuredText with indentation

您可以缩进 reStructuredText 元素以创建层次结构。
通常的缩进会自动从每行的开头删除，以保留相对缩进，
因此您可以缩进多行字符串。

```python
from nicegui import ui

ui.restructured_text('''
    This is an example of a reStructuredText paragraph with several indentation levels.

    You can use multiple levels of indentation to structure your content.
    Each level of indentation represents a different level of hierarchy.

    - Level 1
        - Level 2
            - Level 3
                - Level 4
                    - Level 5
'''
)

ui.run()
```

## reStructuredText with code blocks

您可以使用代码块来显示代码示例。
如果指定语言，代码将进行语法高亮显示。
有关支持的语言列表，请参阅 [此链接](https://docs.typo3.org/m/typo3/docs-how-to-document/main/en-us/WritingReST/Reference/Code/Codeblocks.html#writing-rest-codeblocks-available-lexers)。

```python
from nicegui import ui

ui.restructured_text('''
    .. code-block:: python3

        from nicegui import ui

        ui.label('Hello World!')

        ui.run()
'''
)

ui.run()
```

## reStructuredText with tables

有关 reStructuredText 表格的更多信息，请参阅 [sphinx 文档](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html#tables)。

```python
from nicegui import ui

ui.restructured_text('''
    +-------+-------+---------+--------+
    | A     | B     | A and B | A or B |
    +=======+=======+=========+========+
    | False | False | False   | False  |
    +-------+-------+---------+--------+
    | True  | False | False   | True   |
    +-------+-------+---------+--------+
    | False | True  | False   | True   |
    +-------+-------+---------+--------+
    | True  | True  | True    | True   |
    +-------+-------+---------+--------+
'''
)

ui.run()
```

## Reference

### Initializer

| 参数    | 描述                     |
| :------ | :----------------------- |
| content | 要显示的 ReStructuredText 内容 |

### Properties

**`classes`**`: 'Classes[Self]'`

元素的类。

**`content`**`: BindableProperty`

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

| 参数     | 描述                               |
| :------- | :--------------------------------- |
| path: | 资源路径 (例如：包含 CSS 和 JavaScript 文件的文件夹) |

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

| 参数       | 描述               |
| :--------- | :----------------- |
| name:      | slot 的名称        |
| template:  | slot 的 Vue 模板    |
| return:    | slot               |

**`ancestors`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的祖先。

| 参数            | 描述                                 |
| :-------------- | :----------------------------------- |
| include_self: | 是否在迭代中包含元素本身 |

**`bind_content`**`(target_object: Any, target_name: str = 'content', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

将此元素的内容绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值发生变化时进行。
向后绑定优先进行初始同步。

| 参数           | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| target_object: | 要绑定的对象。                                               |
| target_name:   | 要绑定到的属性的名称。                                         |
| forward:       | 在将值应用于目标之前应用于该值的函数。                         |
| backward:      | 在将值应用于此元素之前应用于该值的函数。                       |

**`bind_content_from`**`(target_object: Any, target_name: str = 'content', backward: Callable[..., Any] = [...]) -> Self`

从目标对象的 target_name 属性绑定此元素的内容。

绑定是单向的，仅从目标到此元素。
更新会立即发生，并在值发生变化时进行。

| 参数           | 描述                                                       |
| :------------- | :--------------------------------------------------------- |
| target_object: | 要绑定的对象。                                             |
| target_name:   | 要绑定到的属性的名称。                                       |
| backward:      | 在将值应用于此元素之前应用于该值的函数。                     |

**`bind_content_to`**`(target_object: Any, target_name: str = 'content', forward: Callable[..., Any] = [...]) -> Self`

将此元素的内容绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并在值发生变化时进行。

| 参数           | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| target_object: | 要绑定的对象。                                               |
| target_name:   | 要绑定到的属性的名称。                                         |
| forward:       | 在将值应用于目标之前应用于该值的函数。                         |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值发生变化时进行。
向后绑定优先进行初始同步。

| 参数           | 描述                                                                |
| :------------- | :------------------------------------------------------------------ |
| target_object: | 要绑定的对象。                                                       |
| target_name:   | 要绑定到的属性的名称。                                                 |
| forward:       | 在将值应用于目标之前应用于该值的函数。                                 |
| backward:      | 在将值应用于此元素之前应用于该值的函数。                               |
| value:        | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从目标对象的 target_name 属性绑定此元素的可见性。

绑定是单向的，仅从目标到此元素。
更新会立即发生，并在值发生变化时进行。

| 参数           | 描述                                                                |
| :------------- | :------------------------------------------------------------------ |
| target_object: | 要绑定的对象。                                                       |
| target_name:   | 要绑定到的属性的名称。                                                 |
| backward:      | 在将值应用于此元素之前应用于该值的函数。                               |
| value:        | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是单向的，仅从该元素到目标。
更新会立即发生，并在值发生变化时进行。

| 参数           | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| target_object: | 要绑定的对象。                                               |
| target_name:   | 要绑定到的属性的名称。                                         |
| forward:       | 在将值应用于目标之前应用于该值的函数。                         |

**`clear`**`() -> None`

删除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素都将共享这些 HTML 类。
这些必须在元素实例化之前定义。

| 参数      | 描述                                     |
| :-------- | :--------------------------------------- |
| add:    | 以空格分隔的类字符串                       |
| remove: | 从元素中删除的以空格分隔的类字符串         |
| toggle: | 要切换的以空格分隔的类字符串                 |
| replace: | 用于替换现有类的以空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认 props。

这允许使用 [Quasar](https://quasar.dev/) props 修改元素的外观或其布局。
由于 props 只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素都将共享这些 props。
这些必须在元素实例化之前定义。

如果未指定值，则假定布尔属性为 `True`。

| 参数     | 描述                                                     |
| :------- | :------------------------------------------------------- |
| add:    | 要添加的布尔值或 key=value 对的以空格分隔的列表 |
| remove: | 要删除的属性键的以空格分隔的列表                         |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。
此类的所有元素都将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

| 参数      | 描述                                     |
| :-------- | :--------------------------------------- |
| add:    | 要添加到元素的分号分隔的样式列表             |
| remove: | 从元素中删除的分号分隔的样式列表             |
| replace: | 用于替换现有样式列表的分号分隔的样式列表 |

**`delete`**`() -> None`

删除元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

| 参数            | 描述                                 |
| :-------------- | :----------------------------------- |
| include_self: | 是否在迭代中包含元素本身 |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

应该等待此函数，以便正确返回计算的属性。

| 参数        | 描述                                     |
| :---------- | :--------------------------------------- |
| prop_name: | 计算属性的名称                           |
| timeout:    | 等待响应的最长时间（默认：1 秒）         |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于使用 [ElementFilter](/documentation/element_filter) 查询元素，
这在测试中大量使用，
但也可以用于减少全局变量的数量或传递依赖项。

| 参数       | 描述                                            |
| :--------- | :---------------------------------------------- |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

| 参数             | 描述                                                       |
| :--------------- | :--------------------------------------------------------- |
| target_container: | 要将元素移动到的容器（默认：父容器）                             |
| target_index:   | 目标 slot 中的索引（默认：附加到末尾）                       |
| target_slot:    | 目标容器中的 slot（默认：默认 slot）                           |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅事件。

| 参数            | 描述                                                                                             |
| :-------------- | :----------------------------------------------------------------------------------------------- |
| type:          | 事件的名称（例如，“click”、“mousedown”或“update:model-value”）                                      |
| handler:       | 事件发生时调用的回调函数                                                                                 |
| args:          | 事件消息中包含的参数，发送给事件处理程序（默认值：`None`，表示所有）                                                   |
| throttle:      | 事件发生之间的最短时间（以秒为单位）（默认值：0.0）                                                      |
| leading_events: | 是否在第一次事件发生时立即触发事件处理程序（默认值：`True`）                                             |
| trailing_events: | 是否在最后一次事件发生后触发事件处理程序（默认值：`True`）                                              |
| js_handler:    | 事件发生时执行的 JavaScript 代码，例如 `(evt) => alert(evt)`（默认值：`None`） |

**`remove`**`(element: Union[Element, int]) -> None`

删除子元素。

| 参数      | 描述                         |
| :-------- | :--------------------------- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果等待该函数，则返回方法调用的结果。
否则，将执行该方法，而不等待响应。

| 参数      | 描述                                     |
| :-------- | :--------------------------------------- |
| name:   | 方法的名称                                 |
| args:    | 传递给方法的参数                           |
| timeout: | 等待响应的最长时间（默认：1 秒）         |

**`set_content`**`(content: str) -> None`

设置此元素的内容。

| 参数      | 描述           |
| :-------- | :------------- |
| content: | 新的内容。 |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

| 参数      | 描述                                 |
| :-------- | :----------------------------------- |
| visible: | 元素是否应该可见。 |

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

| 参数    | 描述         |
| :------ | :----------- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

在客户端更新元素。

### Inheritance

*   `Markdown`
*   `ContentElement`
*   `Element`
*   `Visibility`
