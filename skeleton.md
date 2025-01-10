# ui.skeleton

## Skeleton

此元素基于 Quasar 的 [QSkeleton](https://quasar.dev/vue-components/skeleton) 组件。它用作卡片、菜单和其他组件容器中加载内容的占位符。有关可用类型的列表，请参阅 [Quasar 文档](https://quasar.dev/vue-components/skeleton/#predefined-types)。

| 选项 | 描述 |
| --- | --- |
| type: | 要显示的骨架类型（默认值：“rect”） |
| tag: | 用于此元素的 HTML 标签（默认值：“div”） |
| animation: | 骨架占位符的动画效果（默认值：“wave”） |
| animation_speed: | 动画速度，以秒为单位（默认值：1.5） |
| square: | 是否移除 border-radius 以使边框为方形（默认值：`False`） |
| bordered: | 是否向组件应用默认边框（默认值：`False`） |
| size: | CSS 单位的大小（覆盖 `width` 和 `height`） |
| width: | CSS 单位的宽度（如果设置了 `size`，则会被覆盖） |
| height: | CSS 单位的高度（如果设置了 `size`，则会被覆盖） |

```python
from nicegui import ui

ui.skeleton().classes('w-full')

ui.run()
```

## Styling and animation

可以设置 `square` 和 `bordered` 参数为 `True`，以删除 border-radius 并向骨架添加边框。

可以将 `animation` 参数设置为 "pulse"、"wave"、"pulse-x"、"pulse-y"、"fade"、"blink" 或 "none" 来更改动画效果。默认值为 "wave"。

```python
from nicegui import ui

ui.skeleton('QToolbar', square=True, bordered=True, animation='pulse-y') \
    .classes('w-full')

ui.run()
```

## YouTube Skeleton

这是一个 YouTube 视频的示例骨架。

```python
from nicegui import ui

with ui.card().tight().classes('w-full'):
    ui.skeleton(square=True, animation='fade', height='150px', width='100%')
    with ui.card_section().classes('w-full'):
        ui.skeleton('text').classes('text-subtitle1')
        ui.skeleton('text').classes('text-subtitle1 w-1/2')
        ui.skeleton('text').classes('text-caption')

ui.run()
```
