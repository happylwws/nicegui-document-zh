# Page Layout

## Auto-context

为了允许编写直观的 UI 描述，NiceGUI 会自动跟踪创建元素的上下文。
这意味着没有显式的 `parent` 参数。
而是使用 `with` 语句定义父上下文。
它也被传递给事件处理程序和计时器。

在演示中，标签“Card content”被添加到卡片中。
并且由于 `ui.button` 也被添加到卡片中，标签“Click!” 也将在此上下文中创建。
标签“Tick!”，在 1 秒后添加一次，也被添加到卡片中。

这种设计决策允许轻松创建模块化组件，这些组件在 UI 中移动后仍然可以工作。
例如，你可以将标签和按钮移动到其他地方，也许将它们包装在另一个容器中，代码仍然可以工作。

```python
from nicegui import ui

with ui.card():
    ui.label('Card content')
    ui.button('Add label', on_click=lambda: ui.label('Click!'))
    ui.timer(1.0, lambda: ui.label('Tick!'), once=True)

ui.run()
```

## Card

此元素基于 Quasar 的 [QCard](https://quasar.dev/vue-components/card) 组件。
它提供了一个带有阴影的容器。

注意:
与此元素相反，
原始的 QCard 默认没有内边距，并隐藏嵌套元素的外边框和阴影。
如果你想要原始的行为，请使用 `tight` 方法。

| 参数 | 描述 |
| --- | --- |
| align_items: | 卡片中项目的对齐方式 ("start", "end", "center", "baseline", or "stretch"; 默认: `None`) |

```python
from nicegui import ui

with ui.card().tight():
    ui.image('https://picsum.photos/id/684/640/360')
    with ui.card_section():
        ui.label('Lorem ipsum dolor sit amet, consectetur adipiscing elit, ...')

ui.run()
```

See [more...](#card)

## Column Element

提供一个容器，该容器将其子元素排列成一列。

| 参数 | 描述 |
| --- | --- |
| wrap: | 是否换行内容 (默认: `False`) |
| align_items: | 列中项目的对齐方式 ("start", "end", "center", "baseline", or "stretch"; 默认: `None`) |

```python
from nicegui import ui

with ui.column():
    ui.label('label 1')
    ui.label('label 2')
    ui.label('label 3')

ui.run()
```

See [more...](#column_element)

## Row Element

提供一个容器，该容器将其子元素排列成一行。

| 参数 | 描述 |
| --- | --- |
| wrap: | 是否换行内容 (默认: `True`) |
| align_items: | 行中项目的对齐方式 ("start", "end", "center", "baseline", or "stretch"; 默认: `None`) |

```python
from nicegui import ui

with ui.row():
    ui.label('label 1')
    ui.label('label 2')
    ui.label('label 3')

ui.run()
```

See [more...](#row_element)

## Grid Element

提供一个容器，该容器将其子元素排列成网格。

| 参数 | 描述 |
| --- | --- |
| rows: | 网格中的行数或带有 grid-template-rows CSS 属性的字符串（例如“auto 1fr”） |
| columns: | 网格中的列数或带有 grid-template-columns CSS 属性的字符串（例如“auto 1fr”） |

```python
from nicegui import ui

with ui.grid(columns=2):
    ui.label('Name:')
    ui.label('Tom')

    ui.label('Age:')
    ui.label('42')

    ui.label('Height:')
    ui.label('1.80m')

ui.run()
```

See [more...](#grid_element)

## List

基于 Quasar 的 [QList](https://quasar.dev/vue-components/list-and-list-items#qlist-api) 组件的列表元素。
它为 `ui.item` 元素提供了一个容器。

```python
from nicegui import ui

with ui.list().props('dense separator'):
    ui.item('3 Apples')
    ui.item('5 Bananas')
    ui.item('8 Strawberries')
    ui.item('13 Walnuts')

ui.run()
```

See [more...](#list)

## Clear Containers

要从行、列或卡片容器中删除所有元素，可以使用以下命令

```
container.clear()
```

或者，你可以通过调用以下命令删除单个元素

- `container.remove(element: Element)`,
- `container.remove(index: int)`, or
- `element.delete()`.

```python
from nicegui import ui

container = ui.row()

def add_face():
    with container:
        ui.icon('face')
add_face()

ui.button('Add', on_click=add_face)
ui.button('Remove', on_click=lambda: container.remove(0) if list(container) else None)
ui.button('Clear', on_click=container.clear)

ui.run()
```

## Teleport

一个允许我们将组件中的内容传输到页面上任何位置的元素。

| 参数 | 描述 |
| --- | --- |
| to: | Teleport 内容的目标元素的 NiceGUI 元素或 CSS 选择器 |

```python
from nicegui import ui

markdown = ui.markdown('Enter your **name**!')

def inject_input():
    with ui.teleport(f'#c{markdown.id} strong'):
        ui.input('name').classes('inline-flex').props('dense outlined')

ui.button('inject input', on_click=inject_input)

ui.run()
```

See [more...](#teleport)

## Expansion Element

提供一个基于 Quasar 的 [QExpansionItem](https://quasar.dev/vue-components/expansion-item) 组件的可展开容器。

| 参数 | 描述 |
| --- | --- |
| text: | 标题文本 |
| caption: | 可选的标题（或子标签）文本 |
| icon: | 可选图标 (默认: None) |
| group: | 用于组内协调打开/关闭状态的可选组名称，也称为“手风琴模式” |
| value: | 是否应在创建时打开扩展 (默认: `False`) |
| on_value_change: | 当值更改时执行的回调 |

```python
from nicegui import ui

with ui.expansion('Expand!', icon='work').classes('w-full'):
    ui.label('inside the expansion')

ui.run()
```

See [more...](#expansion_element)

## Scroll Area

一种通过封装内容来定制滚动条的方法。
此元素公开了 Quasar [ScrollArea](https://quasar.dev/vue-components/scroll-area/) 组件。

| 参数 | 描述 |
| --- | --- |
| on_scroll: | 当滚动位置更改时要调用的函数 |

```python
from nicegui import ui

with ui.row():
    with ui.scroll_area().classes('w-32 h-32 border'):
        ui.label('I scroll. ' * 20)
    with ui.column().classes('p-4 w-32 h-32 border'):
        ui.label('I will not scroll. ' * 10)

ui.run()
```

See [more...](#scroll_area)

## Separator

此元素基于 Quasar 的 [QSeparator](https://quasar.dev/vue-components/separator) 组件。

它充当卡片、菜单和其他组件容器的分隔符，类似于 HTML 的 `<hr>` 标签。

```python
from nicegui import ui

ui.label('text above')
ui.separator()
ui.label('text below')

ui.run()
```

See [more...](#separator)

## Space

此元素基于 Quasar 的 [QSpace](https://quasar.dev/vue-components/space) 组件。

它的目的是简单地填充 flexbox 元素内的所有可用空间。

```python
from nicegui import ui

with ui.row().classes('w-full border'):
    ui.label('Left')
    ui.space()
    ui.label('Right')

ui.run()
```

See [more...](#space)

## Skeleton

此元素基于 Quasar 的 [QSkeleton](https://quasar.dev/vue-components/skeleton) 组件。
它充当卡片、菜单和其他组件容器中加载内容的占位符。
有关可用类型的列表，请参阅 [Quasar 文档](https://quasar.dev/vue-components/skeleton/#predefined-types)。

| 参数 | 描述 |
| --- | --- |
| type: | 要显示的骨架类型（默认：“rect”） |
| tag: | 用于此元素的 HTML 标签（默认：“div”） |
| animation: | 骨架占位符的动画效果（默认：“wave”） |
| animation_speed: | 动画速度（以秒为单位）（默认：1.5） |
| square: | 是否移除边框半径以使边框为方形（默认：`False`） |
| bordered: | 是否将默认边框应用于组件（默认：`False`） |
| size: | CSS 单位的大小（覆盖 `width` 和 `height`） |
| width: | CSS 单位的宽度（如果设置了 `size`，则会被覆盖） |
| height: | CSS 单位的高度（如果设置了 `size`，则会被覆盖） |

```python
from nicegui import ui

ui.skeleton().classes('w-full')

ui.run()
```

See [more...](#skeleton)

## Splitter

`ui.splitter` 元素将屏幕空间划分为可调整大小的部分，从而在你的应用程序中实现灵活和响应式的布局。

基于 Quasar 的 Splitter 组件：
[Splitter](https://quasar.dev/vue-components/splitter)

它提供三个可自定义的插槽，`before`、`after` 和 `separator`，
可用于在分隔符中嵌入其他元素。

| 参数 | 描述 |
| --- | --- |
| horizontal: | 是否水平而不是垂直分割 |
| limits: | 两个数字，表示两个面板的最小和最大拆分大小 |
| value: | 第一个面板的大小（如果使用 reverse，则为第二个面板的大小） |
| reverse: | 是否将模型大小应用于第二个面板而不是第一个面板 |
| on_change: | 当用户释放分隔符时调用的回调 |

```python
from nicegui import ui

with ui.splitter() as splitter:
    with splitter.before:
        ui.label('This is some content on the left hand side.').classes('mr-2')
    with splitter.after:
        ui.label('This is some content on the right hand side.').classes('ml-2')

ui.run()
```

See [more...](#splitter)

## Tabs

元素 `ui.tabs`、`ui.tab`、`ui.tab_panels` 和 `ui.tab_panel` 类似于
[Quasar 的标签](https://quasar.dev/vue-components/tabs) 和
[标签面板](https://quasar.dev/vue-components/tab-panels) API。

`ui.tabs` 创建一个标签容器。例如，这可以放在 `ui.header` 中。
`ui.tab_panels` 创建一个带有实际内容的选项卡面板容器。
每个 `ui.tab_panel` 都与一个 `ui.tab` 元素关联。

```python
from nicegui import ui

with ui.tabs().classes('w-full') as tabs:
    one = ui.tab('One')
    two = ui.tab('Two')
with ui.tab_panels(tabs, value=two).classes('w-full'):
    with ui.tab_panel(one):
        ui.label('First tab')
    with ui.tab_panel(two):
        ui.label('Second tab')

ui.run()
```

See [more...](#tabs)

## Stepper

此元素表示 [Quasar 的 QStepper](https://quasar.dev/vue-components/stepper#qstepper-api) 组件。
它包含各个步骤。

为避免在切换步骤时出现动态元素的问题，
此元素使用 Vue 的 [keep-alive](https://vuejs.org/guide/built-ins/keep-alive.html) 组件。
如果客户端性能是一个问题，你可以禁用此功能。

| 参数 | 描述 |
| --- | --- |
| value: | 要最初选择的步骤的 `ui.step` 或名称（默认：`None`，表示第一步） |
| on_value_change: | 当所选步骤更改时要执行的回调 |
| keep_alive: | 是否在内容上使用 Vue 的 keep-alive 组件（默认：`True`） |

```python
from nicegui import ui

with ui.stepper().props('vertical').classes('w-full') as stepper:
    with ui.step('Preheat'):
        ui.label('Preheat the oven to 350 degrees')
        with ui.stepper_navigation():
            ui.button('Next', on_click=stepper.next)
    with ui.step('Ingredients'):
        ui.label('Mix the ingredients')
        with ui.stepper_navigation():
            ui.button('Next', on_click=stepper.next)
            ui.button('Back', on_click=stepper.previous).props('flat')
    with ui.step('Bake'):
        ui.label('Bake for 20 minutes')
        with ui.stepper_navigation():
            ui.button('Done', on_click=lambda: ui.notify('Yay!', type='positive'))
            ui.button('Back', on_click=stepper.previous).props('flat')

ui.run()
```

See [more...](#stepper)

## Timeline

此元素表示 [Quasar 的 QTimeline](https://quasar.dev/vue-components/timeline#qtimeline-api) 组件。

| 参数 | 描述 |
| --- | --- |
| side: | 侧面（“左”或“右”；默认：“左”）。 |
| layout: | 布局（“dense”、“comfortable”或“loose”；默认：“dense”）。 |
| color: | 图标的颜色。 |

```python
from nicegui import ui

with ui.timeline(side='right'):
    ui.timeline_entry('Rodja and Falko start working on NiceGUI.',
                      title='Initial commit',
                      subtitle='May 07, 2021')
    ui.timeline_entry('The first PyPI package is released.',
                      title='Release of 0.1',
                      subtitle='May 14, 2021')
    ui.timeline_entry('Large parts are rewritten to remove JustPy '
                      'and to upgrade to Vue 3 and Quasar 2.',
                      title='Release of 1.0',
                      subtitle='December 15, 2022',
                      icon='rocket')

ui.run()
```

See [more...](#timeline)

## Carousel

此元素表示 [Quasar 的 QCarousel](https://quasar.dev/vue-components/carousel#qcarousel-api) 组件。
它包含单独的轮播幻灯片。

| 参数 | 描述 |
| --- | --- |
| value: | 要最初选择的幻灯片的 `ui.carousel_slide` 或名称（默认：`None`，表示第一张幻灯片） |
| on_value_change: | 当所选幻灯片更改时要执行的回调 |
| animated: | 是否为幻灯片过渡添加动画效果（默认：`False`） |
| arrows: | 是否显示用于手动幻灯片导航的箭头（默认：`False`） |
| navigation: | 是否显示用于手动幻灯片导航的导航点（默认：`False`） |

```python
from nicegui import ui

with ui.carousel(animated=True, arrows=True, navigation=True).props('height=180px'):
    with ui.carousel_slide().classes('p-0'):
        ui.image('https://picsum.photos/id/30/270/180').classes('w-[270px]')
    with ui.carousel_slide().classes('p-0'):
        ui.image('https://picsum.photos/id/31/270/180').classes('w-[270px]')
    with ui.carousel_slide().classes('p-0'):
        ui.image('https://picsum.photos/id/32/270/180').classes('w-[270px]')

ui.run()
```

See [more...](#carousel)

## Pagination

一个包装 Quasar 的 [QPagination](https://quasar.dev/vue-components/pagination) 组件的分页元素。

| 参数 | 描述 |
| --- | --- |
| min: | 最小页码 |
| max: | 最大页码 |
| direction_links: | 是否显示第一页/最后一页链接 |
| value: | 初始页（如果未提供值，则默认为 `min`） |
| on_change: | 当值更改时要调用的回调 |

```python
from nicegui import ui

p = ui.pagination(1, 5, direction_links=True)
ui.label().bind_text_from(p, 'value', lambda v: f'Page {v}')

ui.run()
```

See [more...](#pagination)

## Menu

基于 Quasar 的 [QMenu](https://quasar.dev/vue-components/menu) 组件创建一个菜单。
该菜单应放置在应显示它的元素内部。

高级提示：
使用 `auto-close` 属性在不进行服务器往返的情况下直接自动关闭任何单击事件上的菜单。

| 参数 | 描述 |
| --- | --- |
| value: | 菜单是否已打开（默认：`False`） |

```python
from nicegui import ui

with ui.row().classes('w-full items-center'):
    result = ui.label().classes('mr-auto')
    with ui.button(icon='menu'):
        with ui.menu() as menu:
            ui.menu_item('Menu item 1', lambda: result.set_text('Selected item 1'))
            ui.menu_item('Menu item 2', lambda: result.set_text('Selected item 2'))
            ui.menu_item('Menu item 3 (keep open)',
                         lambda: result.set_text('Selected item 3'), auto_close=False)
            ui.separator()
            ui.menu_item('Close', menu.close)

ui.run()
```

See [more...](#menu)

## Context Menu

基于 Quasar 的 [QMenu](https://quasar.dev/vue-components/menu) 组件创建一个上下文菜单。
上下文菜单应放置在应显示它的元素内部。
当用户右键单击该元素时，它会自动打开并出现在鼠标位置。

```python
from nicegui import ui

with ui.image('https://picsum.photos/id/377/640/360'):
    with ui.context_menu():
        ui.menu_item('Flip horizontally')
        ui.menu_item('Flip vertically')
        ui.separator()
        ui.menu_item('Reset', auto_close=False)

ui.run()
```

See [more...](#context_menu)

## Tooltip

此元素基于 Quasar 的 [QTooltip](https://quasar.dev/vue-components/tooltip) 组件。
它可以放置在另一个元素中，以在悬停时显示其他信息。

除了将字符串作为第一个参数传递之外，你还可以在工具提示中嵌套其他元素。

| 参数 | 描述 |
| --- | --- |
| text: | 工具提示的内容 (默认: '') |

```python
from nicegui import ui

with ui.button(icon='thumb_up'):
    ui.tooltip('I like this').classes('bg-green')

ui.run()
```

See [more...](#tooltip)

## Notification

在屏幕上显示通知。

| 参数 | 描述 |
| --- | --- |
| message: | 通知的内容 |
| position: | 屏幕上的位置 ("top-left", "top-right", "bottom-left", "bottom-right", "top", "bottom", "left", "right" or "center", 默认: "bottom") |
| close_button: | 用于关闭通知的可选按钮标签（默认：`False`） |
| type: | 可选类型（“positive”、“negative”、“warning”、“info” 或“ongoing”） |
| color: | 可选颜色名称 |
| multi_line: | 启用多行通知 |

注意：你可以根据 [Quasar 的通知 API](https://quasar.dev/quasar-plugins/notify#notify-api) 传递其他关键字参数。

```python
from nicegui import ui

ui.button('Say hi!', on_click=lambda: ui.notify('Hi!', close_button='OK'))

ui.run()
```

See [more...](#notification)

## Notification element

在屏幕上显示通知。
与 `ui.notify` 相比，此元素允许在显示通知后更新通知消息和其他属性。
可以使用 `dismiss()` 删除通知。

| 参数 | 描述 |
| --- | --- |
| message: | 通知的内容 |
| position: | 屏幕上的位置 ("top-left", "top-right", "bottom-left", "bottom-right", "top", "bottom", "left", "right" or "center", 默认: "bottom") |
| close_button: | 用于关闭通知的可选按钮标签（默认：`False`） |
| type: | 可选类型（“positive”、“negative”、“warning”、“info” 或“ongoing”） |
| color: | 可选颜色名称 |
| multi_line: | 启用多行通知 |
| icon: | 要在通知中显示的可选图标名称（默认：`None`） |
| spinner: | 在通知中显示微调器（默认值：False） |
| timeout: | 可选超时时间（以秒为单位），之后将关闭通知（默认值：5.0） |
| on_dismiss: | 关闭通知时要调用的可选回调 |
| options: | 带有所有选项的可选字典（覆盖所有其他参数） |

注意：你可以根据 [Quasar 的通知 API](https://quasar.dev/quasar-plugins/notify#notify-api) 传递其他关键字参数。

```python
import asyncio
from nicegui import ui

async def compute():
    n = ui.notification(timeout=None)
    for i in range(10):
        n.message = f'Computing {i/10:.0%}'
        n.spinner = True
        await asyncio.sleep(0.2)
    n.message = 'Done!'
    n.spinner = False
    await asyncio.sleep(1)
    n.dismiss()

ui.button('Compute', on_click=compute)

ui.run()
```

See [more...](#notification_element)

## Dialog

基于 Quasar 的 [QDialog](https://quasar.dev/vue-components/dialog) 组件创建一个对话框。
默认情况下，可以通过单击或按 ESC 关闭它。
要使其保持持久性，请在对话框元素上设置 `.props('persistent')`。

注意：对话框是一个元素。
这意味着它在关闭时不会被删除，而只是被隐藏。
你应该只创建一次并重复使用，或者在关闭后使用 `.clear()` 删除它。

| 参数 | 描述 |
| --- | --- |
| value: | 是否应在创建时打开对话框（默认值：`False`） |

```python
from nicegui import ui

with ui.dialog() as dialog, ui.card():
    ui.label('Hello world!')
    ui.button('Close', on_click=dialog.close)

ui.button('Open a dialog', on_click=dialog.open)

ui.run()
```

See [more...](#dialog)
