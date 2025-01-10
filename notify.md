# ui.notify

## Notification

在屏幕上显示一个通知。

| 参数         | 描述                                                                                                                                         |
|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| message:     | 通知的内容                                                                                                                                   |
| position:    | 屏幕上的位置 ("top-left", "top-right", "bottom-left", "bottom-right", "top", "bottom", "left", "right" 或 "center", 默认: "bottom") |
| close_button: | 一个可选的按钮标签，用于关闭通知 (默认: `False`)                                                                                               |
| type:        | 可选的类型 ("positive", "negative", "warning", "info" 或 "ongoing")                                                                      |
| color:       | 可选的颜色名称                                                                                                                                 |
| multi_line:  | 启用多行通知                                                                                                                                 |

注意：你可以根据 [Quasar 的 Notify API](https://quasar.dev/quasar-plugins/notify#notify-api) 传递额外的关键字参数。

```python
from nicegui import ui

ui.button('Say hi!', on_click=lambda: ui.notify('Hi!', close_button='OK'))

ui.run()
```

## Notification Types

有不同的类型可以用来指示通知的性质。

```python
from nicegui import ui

ui.button('negative', on_click=lambda: ui.notify('error', type='negative'))
ui.button('positive', on_click=lambda: ui.notify('success', type='positive'))
ui.button('warning', on_click=lambda: ui.notify('warning', type='warning'))

ui.run()
```

## Multiline Notifications

要允许通知文本跨越多行，设置 `multi_line=True` 就足够了。
如果需要手动换行符 (例如 `\n`)，则需要定义一个 CSS 样式并将其传递给通知，如示例所示。

```python
from nicegui import ui

ui.html('<style>.multi-line-notification { white-space: pre-line; }</style>')
ui.button('show', on_click=lambda: ui.notify(
    'Lorem ipsum dolor sit amet, consectetur adipisicing elit. \n'
    'Hic quisquam non ad sit assumenda consequuntur esse inventore officia. \n'
    'Corrupti reiciendis impedit vel, '
    'fugit odit quisquam quae porro exercitationem eveniet quasi.',
    multi_line=True,
    classes='multi-line-notification',
))

ui.run()
```
