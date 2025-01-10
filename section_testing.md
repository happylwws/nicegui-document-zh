# *Testing*

## Project Structure

NiceGUI 包提供了一个 [pytest 插件](https://docs.pytest.org/en/stable/how-to/writing_plugins.html)，可以通过 `pytest_plugins = ['nicegui.testing.plugin']` 激活。这使得专门的 [fixtures](https://docs.pytest.org/en/stable/explanation/fixtures.html) 可用于测试你的 NiceGUI 用户界面。使用 [`screen` fixture](/documentation/screen) 你可以通过无头浏览器运行测试（较慢），而使用 [`user` fixture](/documentation/user) 则可以在 Python 中完全模拟（较快）。如果你只需要一种测试 fixture，你也可以使用插件 `nicegui.testing.user_plugin` 或 `nicegui.testing.screen_plugin`。

有多种方式来构建你的项目和测试。这里我们只介绍两种我们认为有用的方法，一种用于[小型应用和实验](/documentation/project_structure#simple)，另一种用于[大型项目的模块化方法](/documentation/project_structure#modular)。你可以在 [pytest 文档](https://docs.pytest.org/en/stable/contents.html) 中找到更多信息。

See [more...](project_structure)

## User Fixture

我们建议尽可能使用 `user` fixture 而不是 [`screen` fixture](/documentation/screen)，因为通过 `pytest_plugins = ['nicegui.testing.user_plugin']` 加载时，执行速度与单元测试一样快，并且不需要 Selenium 作为依赖项。`user` fixture 移除了浏览器，并将其替换为完全在 Python 中进行的轻量级模拟。有关设置的描述，请参见[项目结构](/documentation/project_structure)。

你可以断言“看到”特定的元素或内容，点击按钮，在输入框中键入并触发事件。我们的目标是提供一个友好的 API 来编写像故事一样易于理解的验收测试。由于执行速度快，传统的 [测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)，其中 UI 测试被认为是缓慢且昂贵的，不再适用。

```python
await user.open('/')
user.find('Username').type('user1')
user.find('Password').type('pass1').trigger('keydown.enter')
await user.should_see('Hello user1!')
user.find('logout').click()
await user.should_see('Log in')
```

**注意：** `user` fixture 是一个相当新的功能，仍然缺少一些特性。请在 [GitHub 上](https://github.com/zauberzeug/nicegui/discussions/new?category=ideas-feature-requests) 的单独功能请求中告知我们。

See [more...](user)

## Screen Fixture

`screen` fixture 启动一个真实的（无头）浏览器来与你的应用程序交互。只有在需要测试特定于浏览器的行为时才需要这样做。NiceGUI 本身已经使用此 fixture 进行了全面测试，以确保每个组件都能按预期工作。所以只有在必要时才使用它。

```python
from selenium.webdriver.common.keys import Keys

screen.open('/')
screen.type(Keys.TAB)  # to focus on the first input
screen.type('user1')
screen.type(Keys.TAB)  # to focus the second input
screen.type('pass1')
screen.click('Log in')
screen.should_contain('Hello user1!')
screen.click('logout')
screen.should_contain('Log in')
```

See [more...](screen)
