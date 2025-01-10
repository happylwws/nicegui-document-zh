# Project Structure

## Project Structure

NiceGUI 包提供了一个 [pytest 插件](https://docs.pytest.org/en/stable/how-to/writing_plugins.html)，可以通过 `pytest_plugins = ['nicegui.testing.plugin']` 激活。这使得专门的[fixtures](https://docs.pytest.org/en/stable/explanation/fixtures.html) 可用于测试你的 NiceGUI 用户界面。使用 [ `screen` fixture](/documentation/screen)，你可以通过无头浏览器运行测试（速度较慢），而使用 [`user` fixture](/documentation/user)，则可以在 Python 中完全模拟测试（速度较快）。如果你只需要一种测试 fixture，你也可以使用插件 `nicegui.testing.user_plugin` 或 `nicegui.testing.screen_plugin`。

有多种方法可以构建你的项目和测试。这里我们只介绍两种我们认为有用的方法，一种用于[小型应用和实验](#simple)，另一种用于[大型项目的模块化方法](#modular)。你可以在 [pytest 文档](https://docs.pytest.org/en/stable/contents.html) 中找到更多信息。

## Simple

对于小型应用程序和实验，你可以将测试放在单独的文件中，就像我们在 [Chat App](https://github.com/zauberzeug/nicegui/tree/main/examples/chat_app)、[Todo List](https://github.com/zauberzeug/nicegui/tree/main/examples/todo_list/) 和 [Authentication](https://github.com/zauberzeug/nicegui/tree/main/examples/authentication) 示例中所做的那样。为了在测试中正确地重新初始化你的 `main.py`，你需要在代码旁边放置一个空的 `__init__.py` 文件，使其成为一个包，并使用 `module_under_test` 标记来为每个测试自动重新加载你的主文件。另外，不要忘记 `pytest.ini` 文件，以启用 user fixture 的 [`asyncio_mode = auto`](/documentation/user#async_execution) 选项，并确保在 `main.py` 中正确保护 `ui.run()` 调用，以防止服务器在测试期间启动：

```python
from nicegui import ui

def hello() -> None:
    ui.notify('Hello World!')

ui.button('Click me', on_click=hello)

if __name__ in {'__main__', '__mp_main__'}:
    ui.run()
```

```python
import pytest
from nicegui import ui
from nicegui.testing import User
from . import main

pytest_plugins = ['nicegui.testing.user_plugin']

@pytest.mark.module_under_test(main)
async def test_click(user: User) -> None:
    await user.open('/')
    await user.should_see('Click me')
    user.find(ui.button).click()
    await user.should_see('Hello World!')
```

```bash
$ ls
__init__.py         main.py        test_app.py       pytest.ini

$ pytest
===================== test session starts =====================
test_app.py .                                     [100%]
====================== 1 passed in 0.51s =======================
```

## Modular

一种更模块化的方法是为你的代码创建一个包，其中包含一个空的 `__init__.py` 和一个单独的 `tests` 文件夹用于测试。在你的包中，可以使用 `startup.py` 文件来注册页面并执行所有必要的应用程序初始化。然后，根级别的 `main.py` 只导入启动例程并调用 `ui.run()`。根目录中的空 `conftest.py` 文件使带有其 `startup` 例程的包可用于测试。另外，不要忘记 `pytest.ini` 文件，以启用 user fixture 的 [`asyncio_mode = auto`](/documentation/user#async_execution) 选项。

```python
from nicegui import ui, app
from app.startup import startup

app.on_startup(startup)

ui.run()
```

```python
from nicegui import ui

def hello() -> None:
    ui.notify('Hello World!')

def startup() -> None:
    @ui.page('/')
    def index():
        ui.button('Click me', on_click=hello)
```

```python
from nicegui import ui
from nicegui.testing import User
from app.startup import startup

pytest_plugins = ['nicegui.testing.user_plugin']

async def test_click(user: User) -> None:
    startup()
    await user.open('/')
    await user.should_see('Click me')
    user.find(ui.button).click()
    await user.should_see('Hello World!')
```

```bash
$ tree
.
├── main.py
├── pytest.ini
├── app
│   ├── __init__.py
│   └── startup.py
└── tests
    ├── conftest.py
    └── test_app.py
```

你还可以在 `conftest.py` 中定义自己的 fixtures，它们会调用 `startup` 例程。Pytest 有一些魔法可以自动在你的测试中查找和使用这个特殊的 fixture。这样你就可以保持测试的简洁和简单。有关此设置的完整演示，请参见 [pytests example](https://github.com/zauberzeug/nicegui/tree/main/examples/pytests)。

```python
from nicegui import ui
from nicegui.testing import User

async def test_click(user: User) -> None:
    await user.open('/')
    await user.should_see('Click me')
    user.find(ui.button).click()
    await user.should_see('Hello World!')
```

```python
import pytest
from nicegui.testing import User
from app.startup import startup

pytest_plugins = ['nicegui.testing.user_plugin']

@pytest.fixture
def user(user: User) -> User:
    startup()
    return user
```
