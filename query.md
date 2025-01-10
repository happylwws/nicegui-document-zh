# ui.query

## Query Selector

可以使用 `ui.query` 函数来操作诸如文档主体之类的元素。通过查询结果，您可以像使用其他 UI 元素一样添加类、样式和属性。例如，这对于更改页面的背景颜色很有用 (例如 `ui.query('body').classes('bg-green')`)。

| 参数      | 描述                                                     |
| --------- | -------------------------------------------------------- |
| selector: | CSS 选择器 (例如 "body", "#my-id", ".my-class", "div > p") |

```python
from nicegui import ui

def set_background(color: str) -> None:
    ui.query('body').style(f'background-color: {color}')

ui.button('Blue', on_click=lambda: set_background('#ddeeff'))
ui.button('Orange', on_click=lambda: set_background('#ffeedd'))

ui.run()
```

## Set background gradient

设置背景渐变、图像或类似内容非常容易。有关使用 CSS 设置背景的更多信息，请参阅 [w3schools.com](https://www.w3schools.com/cssref/pr_background-image.php)。

```python
from nicegui import ui

ui.query('body').classes('bg-gradient-to-t from-blue-400 to-blue-100')

ui.run()
```

## Modify default page padding

默认情况下，NiceGUI 在页面内容周围提供内置的 padding。您可以使用类选择器 `.nicegui-content` 修改它。

```python
from nicegui import ui

ui.query('.nicegui-content').classes('p-0')
with ui.column().classes('h-screen w-full bg-gray-400 justify-between'):
    ui.label('top left')
    ui.label('bottom right').classes('self-end')

ui.run()
```

## Reference

### Initializer

| 参数      | 描述                                                     |
| --------- | -------------------------------------------------------- |
| selector: | CSS 选择器 (例如 "body", "#my-id", ".my-class", "div > p") |

### Methods

**`classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> Self`

应用、删除、切换或替换 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类会很有帮助。

| 参数      | 描述                                      |
| --------- | ----------------------------------------- |
| add:      | 以空格分隔的类字符串                      |
| remove:   | 要从元素中删除的以空格分隔的类字符串      |
| toggle:   | 要切换的以空格分隔的类字符串              |
| replace:  | 要使用的类字符串，以替换现有的类，空格分隔 |

**`props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> Self`

添加或删除 props。

这允许使用 [Quasar](https://quasar.dev/) props 修改元素的外观或其布局。由于 props 只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。

如果未指定值，则布尔属性假定为 `True`。

| 参数      | 描述                                               |
| --------- | -------------------------------------------------- |
| add:      | 要添加的布尔值或键值对的以空格分隔的列表 |
| remove:   | 要删除的属性键的以空格分隔的列表                     |

**`style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> Self`

应用、删除或替换 CSS 定义。

如果不需要预定义的样式，则删除或替换样式会很有帮助。

| 参数      | 描述                                         |
| --------- | -------------------------------------------- |
| add:      | 要添加到元素的以分号分隔的样式列表           |
| remove:   | 要从元素中删除的以分号分隔的样式列表         |
| replace:  | 要使用的样式列表，以替换现有的样式，分号分隔 |
