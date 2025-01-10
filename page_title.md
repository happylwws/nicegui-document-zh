# ui.page_title

## Page title

为当前客户端设置页面标题。

|  参数 | 描述 |
| --- | --- |
| title: | 页面标题 |

```python
from nicegui import ui

ui.button('Change page title', on_click=lambda: ui.page_title('New Title'))

ui.run()
```
