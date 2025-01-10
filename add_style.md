# ui.add_css

## Add CSS style definitions to the page

此函数可用于向 HTML 页面的头部添加 CSS 样式定义。

| 参数 | 描述 |
| --- | --- |
| content: | CSS 内容（字符串或文件路径） |

```python
from nicegui import ui

ui.add_css('''
    .red {
        color: red;
    }
''')
ui.label('This is red with CSS.').classes('red')

ui.run()
```

## Add SCSS style definitions to the page

此函数可用于向 HTML 页面的头部添加 SCSS 样式定义。

| 参数 | 描述 |
| --- | --- |
| content: | SCSS 内容（字符串或文件路径） |
| indented: | 内容是否缩进（SASS） (默认: `False`) |

```python
from nicegui import ui

ui.add_scss('''
    .green {
        background-color: lightgreen;
        .blue {
            color: blue;
        }
    }
''')
with ui.element().classes('green'):
    ui.label('This is blue on green with SCSS.').classes('blue')

ui.run()
```

## Add SASS style definitions to the page

此函数可用于向 HTML 页面的头部添加 SASS 样式定义。

| 参数 | 描述 |
| --- | --- |
| content: | SASS 内容（字符串或文件路径） |

```python
from nicegui import ui

ui.add_sass('''
    .yellow
        background-color: yellow
        .purple
            color: purple
''')
with ui.element().classes('yellow'):
    ui.label('This is purple on yellow with SASS.').classes('purple')

ui.run()
```
