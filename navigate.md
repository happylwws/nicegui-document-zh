# ui.navigate

## Navigation functions

这些函数允许你在浏览器历史记录中导航以及跳转到外部 URL。

```python
from nicegui import ui

with ui.row():
    ui.button('Back', on_click=ui.navigate.back)
    ui.button('Forward', on_click=ui.navigate.forward)
    ui.button(icon='savings',
              on_click=lambda: ui.navigate.to('https://github.com/sponsors/zauberzeug'))

ui.run()
```

## ui.navigate.to (formerly ui.open)

可以用于以编程方式打开不同的页面或 URL。

当使用 `new_tab` 参数时，浏览器可能会阻止新标签页的打开。
这是一个浏览器设置，不能通过应用程序更改。
你可能需要使用 `ui.link` 及其 `new_tab` 参数来替代。

此功能以前以 `ui.open` 的形式提供，现在已被弃用。

注意：当使用 [auto-index page](documentation/section_pages_routing#auto-index_page)（例如，没有 `@page` 装饰器）时，
所有连接到页面的客户端（即浏览器）都将打开目标 URL，除非指定了套接字。
像按钮点击这样的用户事件会提供这样的套接字。

| 参数 | 描述 |
| --- | --- |
| target: | 页面函数，同一页面上的 NiceGUI 元素或字符串，该字符串是绝对 URL 或来自基本 URL 的相对路径 |
| new_tab: | 是否在新标签页中打开目标（可能被浏览器阻止） |

```python
from nicegui import ui

url = 'https://github.com/zauberzeug/nicegui/'
ui.button('Open GitHub', on_click=lambda: ui.navigate.to(url, new_tab=True))

ui.run()
```
