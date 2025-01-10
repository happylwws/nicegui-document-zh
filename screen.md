# Screen Fixture

## Screen Fixture

`screen` 夹具启动一个真实的（无头）浏览器来与您的应用程序进行交互。
只有在需要测试特定于浏览器的行为时，才需要这样做。
NiceGUI 本身已经使用此夹具进行了彻底的测试，以确保每个组件都能按预期工作。
因此，仅在必要时才使用它。

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

## Web driver

`screen` 夹具在底层使用 Selenium。
目前仅使用 Chrome 驱动程序进行了测试。
为了在测试中自动使用它，我们建议在您的 `pytest.ini` 中添加选项 `--driver Chrome`：

```ini
[pytest]
asyncio_mode = auto
addopts = "--driver Chrome"
```

## Reference

### Properties

**`is_open`**`: bool`

检查浏览器是否已打开。

### Methods

**`assert_py_logger`**`(level: str, message: Union[str, re.Pattern]) -> None`

断言 Python 记录器已收到具有给定级别和文本或正则表达式模式的消息。

**`click`**`(target_text: str) -> selenium.webdriver.remote.webelement.WebElement`

单击包含给定文本的元素。

**`click_at_position`**`(element: selenium.webdriver.remote.webelement.WebElement, x: int, y: int) -> None`

在给定位置单击给定元素。

**`close`**`() -> None`

关闭浏览器。

**`context_click`**`(target_text: str) -> selenium.webdriver.remote.webelement.WebElement`

右键单击包含给定文本的元素。

**`find`**`(text: str) -> selenium.webdriver.remote.webelement.WebElement`

查找包含给定文本的元素。

**`find_all`**`(text: str) -> List[selenium.webdriver.remote.webelement.WebElement]`

查找所有包含给定文本的元素。

**`find_all_by_class`**`(name: str) -> List[selenium.webdriver.remote.webelement.WebElement]`

查找所有具有给定 CSS 类的元素。

**`find_all_by_tag`**`(name: str) -> List[selenium.webdriver.remote.webelement.WebElement]`

查找所有具有给定 HTML 标签的元素。

**`find_by_class`**`(name: str) -> selenium.webdriver.remote.webelement.WebElement`

查找具有给定 CSS 类的元素。

**`find_by_css`**`(selector: str) -> selenium.webdriver.remote.webelement.WebElement`

查找具有给定 CSS 选择器的元素。

**`find_by_tag`**`(name: str) -> selenium.webdriver.remote.webelement.WebElement`

查找具有给定 HTML 标签的元素。

**`find_element`**`(element: nicegui.element.Element) -> selenium.webdriver.remote.webelement.WebElement`

查找给定的 NiceGUI 元素。

**`implicitly_wait`**`(t: float) -> Generator[NoneType, NoneType, NoneType]`

临时更改隐式等待时间。

**`open`**`(path: str, timeout: float = 3.0) -> None`

尝试打开页面，直到服务器准备就绪或超时。

如果服务器尚未运行，则启动它。

**`render_js_logs`**`() -> str`

将浏览器控制台日志呈现为字符串。

**`shot`**`(name: str) -> None`

截取屏幕截图并将其存储在 screenshots 目录中。

**`should_contain`**`(text: str) -> None`

断言页面包含给定的文本。

**`should_contain_input`**`(text: str) -> None`

断言页面包含具有给定值的输入框。

**`should_load_image`**`(image: selenium.webdriver.remote.webelement.WebElement, timeout: float = 2.0) -> None`

断言给定的图像已加载。

**`should_not_contain`**`(text: str, wait: float = 0.5) -> None`

断言页面不包含给定的文本。

**`start_server`**`() -> None`

在单独的线程中启动 Web 服务器。这等效于普通脚本中的 `ui.run()`。

**`stop_server`**`() -> None`

停止 Web 服务器。

**`switch_to`**`(tab_id: int) -> None`

切换到具有给定索引的选项卡，如果该选项卡不存在，则创建它。

**`type`**`(text: str) -> None`

将给定的文本键入到当前聚焦的元素中。

**`wait`**`(t: float) -> None`

等待给定的秒数。

**`wait_for`**`(target: Union[str, Callable[..., bool]]) -> None`

等待直到页面包含给定的文本或满足给定的条件。
