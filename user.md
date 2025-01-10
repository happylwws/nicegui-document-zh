# User Fixture

## User Fixture

我们建议尽可能使用 `user` fixture 而不是 [`screen` fixture](/documentation/screen)，因为当通过 `pytest_plugins = ['nicegui.testing.user_plugin']` 加载时，执行速度与单元测试一样快，并且不需要 Selenium 作为依赖项。`user` fixture 会切断浏览器，并将其替换为完全在 Python 中进行的轻量级模拟。有关设置的描述，请参见[项目结构](/documentation/project_structure)。

你可以断言“看到”特定的元素或内容，单击按钮，在输入框中输入内容并触发事件。我们的目标是提供一个良好的 API 来编写验收测试，使其像一个故事一样易于理解。由于执行速度快，传统的[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)不再适用，在传统测试金字塔中，UI 测试被认为是缓慢且昂贵的。

```python
await user.open('/')
user.find('Username').type('user1')
user.find('Password').type('pass1').trigger('keydown.enter')
await user.should_see('Hello user1!')
user.find('logout').click()
await user.should_see('Log in')
```

**注意：** `user` fixture 是一个相当新的功能，仍然缺少一些特性。请在 GitHub 上通过单独的[功能请求](https://github.com/zauberzeug/nicegui/discussions/new?category=ideas-feature-requests)告知我们。

## Async execution

用户模拟与你的应用程序在同一个异步上下文中运行，以使查询和交互尽可能简单。但也意味着你的测试必须是 `async` 的。我们建议通过在你的项目根目录中创建一个 `pytest.ini` 文件或直接将激活添加到你的 `pyproject.toml` 中来激活 [pytest-asyncio 自动模式](https://pytest-asyncio.readthedocs.io/en/latest/concepts.html#auto-mode)。

```ini
[pytest]
asyncio_mode = auto
```

或者

```toml
[tool.pytest.ini_options]
asyncio_mode = "auto"
```

## Querying

`User` 的查询功能是基于 [ElementFilter](/documentation/element_filter) 构建的。 `user.should_see(...)` 方法和 `user.find(...)` 方法提供了用于筛选内容、[标记](/documentation/element_filter#markers)、类型等的参数。 如果你不提供命名属性，则字符串将与文本内容和标记进行匹配。

```python
with ui.row():
    ui.label('Hello World!').mark('greeting')
    ui.icon('star')
with ui.row():
    ui.label('Hello Universe!')
    ui.input(placeholder='Type here')
```

```python
await user.should_see('greeting')
await user.should_see('star')
await user.should_see('Hello Universe!')
await user.should_see('Type here')
await user.should_see('Hello')
await user.should_see(marker='greeting')
await user.should_see(kind=ui.icon)
```

## Complex elements

有一些具有复杂可视化和交互行为的元素（`ui.upload`、`ui.table` 等）。并非所有这些元素的方面都可以通过 `should_see` 和 `UserInteraction` 进行测试。 不过，你可以使用 `user.find(...)` 获取它们，并在元素本身上进行测试。

```python
def receive_file(e: events.UploadEventArguments):
    content = e.content.read().decode('utf-8')
    reader = csv.DictReader(content.splitlines())
    ui.table(
        columns=[
            {'name': h, 'label': h.capitalize(), 'field': h}
            for h in reader.fieldnames or []
        ],
        rows=list(reader),
    )

ui.upload(on_upload=receive_file)
```

```python
upload = user.find(ui.upload).elements.pop()
upload.handle_uploads([UploadFile(
    BytesIO(b'name,age\nAlice,30\nBob,28'),
    filename='data.csv',
    headers=Headers(raw=[(b'content-type', b'text/csv')]),
)])
table = user.find(ui.table).elements.pop()
assert table.columns == [
    {'name': 'name', 'label': 'Name', 'field': 'name'},
    {'name': 'age', 'label': 'Age', 'field': 'age'},
]
assert table.rows == [
    {'name': 'Alice', 'age': '30'},
    {'name': 'Bob', 'age': '28'},
]
```

## Autocomplete

`user.find(...)` 返回的 `UserInteraction` 对象提供了在找到的元素上触发事件的方法。此演示展示了如何触发 "keydown.tab" 事件以自动完成输入字段。

```python
fruits = ['apple', 'banana', 'cherry']
ui.input(label='fruit', autocomplete=fruits)
```

```python
await user.open('/')
user.find('fruit').type('a').trigger('keydown.tab')
await user.should_see('apple')
```

## Test Downloads

你可以通过检查 `user.downloads.http_responses` 来验证是否触发了下载。 通过等待 `user.downloads.next()`，你可以获取下一个下载响应。

```python
@ui.page('/')
def page():
    def download():
        ui.download(b'Hello', filename='hello.txt')

    ui.button('Download', on_click=download)
```

```python
await user.open('/')
assert len(user.download.http_responses) == 0
user.find('Download').click()
response = await user.download.next()
assert response.text == 'Hello'
```

## Multiple Users

有时仅作为单个用户与 UI 交互是不够的。除了 `user` fixture 之外，我们还提供了 `create_user` fixture，它是一个创建用户的工厂函数。 `User` 实例彼此独立，可以并行与 UI 交互。请参阅我们的 [Chat App 示例](https://github.com/zauberzeug/nicegui/blob/main/examples/chat_app/test_chat_app.py) 获取完整演示。

```python
async def test_chat(create_user: Callable[[], User]) -> None:
    userA = create_user()
    await userA.open('/')
    userB = create_user()
    await userB.open('/')

    userA.find(ui.input).type('from A').trigger('keydown.enter')
    await userB.should_see('from A')
    userB.find(ui.input).type('from B').trigger('keydown.enter')
    await userA.should_see('from A')
    await userA.should_see('from B')
```

## Comparison with the screen fixture

通过切断浏览器，测试执行速度比 [`screen` fixture](/documentation/screen) 快得多。当然，一些功能（如屏幕截图或浏览器特定的行为）不可用。请参阅我们的 [pytests 示例](https://github.com/zauberzeug/nicegui/tree/main/examples/pytests)，其中使用两个 fixture 实现了相同的测试。

## User Reference

### Properties

**`current_layout`**`: 'Element'`

返回当前页面的根布局元素。

### Methods

**`find`**`(target: Union[str, Type[T], None] = None, kind: Optional[Type[T]] = None, marker: Union[str, List[str], None] = None, content: Union[str, List[str], None] = None) -> UserInteraction[T]`

选择元素进行交互。

**`open`**`(path: str, clear_forward_history: bool = True) -> Client`

打开给定的路径。

**`should_not_see`**`(target: Union[str, Type[T], None] = None, kind: Optional[Type[T]] = None, marker: Union[str, List[str], None] = None, content: Union[str, List[str], None] = None, retries: int = 3) -> None`

断言页面不包含具有给定值的输入。

**`should_see`**`(target: Union[str, Type[T], None] = None, kind: Optional[Type[T]] = None, marker: Union[str, List[str], None] = None, content: Union[str, List[str], None] = None, retries: int = 3) -> None`

断言页面包含满足某些过滤规则的元素。

请注意，用户模拟中没有滚动 - 整个页面始终是 *可见的*。由于异步执行，有时预期的元素仅在短时间延迟后才会出现。

默认情况下，`<cite>should_see</cite>` 在失败之前会尝试查找元素三次。可以使用 `<cite>retries</cite>` 参数调整此设置。

## UserInteraction Reference

### Methods

**`clear`**`() -> Self`

清除选定的元素。

注意：所有元素都必须具有 `<tt class="docutils literal">value</tt>` 属性。

**`click`**`() -> Self`

单击选定的元素。

**`trigger`**`(event: str) -> Self`

在模拟用户选择的元素上触发给定的事件。

例如："keydown.enter"、"click" 等。

**`type`**`(text: str) -> Self`

将给定的文本输入到选定的元素中。

注意：所有元素都必须具有 `<tt class="docutils literal">value</tt>` 属性。
