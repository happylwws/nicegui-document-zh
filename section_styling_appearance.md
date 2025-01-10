# Styling & Appearance

## Styling

NiceGUI 使用 [Quasar 框架](https://quasar.dev/)，因此具有其完整的设计能力。
每个 NiceGUI 元素都提供一个 `props` 方法，其内容被传递给 [Quasar 组件](https://justpy.io/quasar_tutorial/introduction/#props-of-quasar-components)：
请查看 [Quasar 文档](https://quasar.dev/vue-components/button#design) 以获取所有样式 props。
以 `:` 开头的 Props 可以包含在客户端求值的 JavaScript 表达式。
您还可以使用 `classes` 方法应用 [Tailwind CSS](https://tailwindcss.com/) 实用程序类。

如果您确实需要应用 CSS，可以使用 `style` 方法。这里的定界符是 `;` 而不是空格。

如果不需要特定的样式，所有这三个函数还提供 `remove` 和 `replace` 参数。

```python
from nicegui import ui

ui.radio(['x', 'y', 'z'], value='x').props('inline color=green')
ui.button(icon='touch_app').props('outline round').classes('shadow-lg')
ui.label('Stylish!').style('color: #6E93D6; font-size: 200%; font-weight: 300')

ui.run()
```

## Try styling NiceGUI elements!

尝试 [Tailwind CSS 类](https://tailwindcss.com/)、[Quasar props](https://justpy.io/quasar_tutorial/introduction/#props-of-quasar-components) 和 CSS 样式如何影响 NiceGUI 元素。

从可用的元素中选择一个并开始设置样式！

```python
from nicegui import ui

element = ui.button('element')
```

`element.classes('` <input type="text"/> `')`

`element.props('` <input type="text"/> `')`

`element.style('` <input type="text"/> `')`

```python
ui.run()
```

## Tailwind CSS

[Tailwind CSS](https://tailwindcss.com/) 是一个用于快速构建自定义用户界面的 CSS 框架。
NiceGUI 提供了一个流畅、自动完成友好的界面，用于向 UI 元素添加 Tailwind 类。

您可以通过导航 `tailwind` 属性的方法来发现可用的类。
构建器模式允许您将多个类链接在一起（如“Label A”所示）。
您还可以使用类列表调用 `tailwind` 属性（如“Label B”所示）。

虽然这与使用 `classes` 方法非常相似，但由于自动完成，它对于 Tailwind 类更方便。

最后但并非最不重要的一点是，您还可以预定义样式并将其应用于多个元素（标签 C 和 D）。

请注意，有时 Tailwind 会被 Quasar 样式覆盖，例如，当使用 `ui.button('Button').tailwind('bg-red-500')` 时。
这是一个已知的限制，并非完全在我们的控制之下。
但我们尝试提供像 `color` 参数这样的解决方案：`ui.button('Button', color='red-500')`。

```python
from nicegui import Tailwind, ui

ui.label('Label A').tailwind.font_weight('extrabold').text_color('blue-600').background_color('orange-200')
ui.label('Label B').tailwind('drop-shadow', 'font-bold', 'text-green-600')

red_style = Tailwind().text_color('red-600').font_weight('bold')
label_c = ui.label('Label C')
red_style.apply(label_c)
ui.label('Label D').tailwind(red_style)

ui.run()
```

## Tailwind CSS Layers

Tailwind CSS 的 `@layer` 指令允许您定义可在 HTML 中使用的自定义类。
NiceGUI 通过允许您将自定义类添加到 `components` 层来支持此功能。
这样，您可以定义自己的类并在 UI 元素中使用它们。
在下面的示例中，我们定义了一个自定义类 `blue-box` 并将其应用于两个标签。
请注意，样式标签的类型为 `text/tailwindcss`，而不是 `text/css`。

```python
from nicegui import ui

ui.add_head_html('''
    <style type="text/tailwindcss">
        @layer components {
            .blue-box {
                @apply bg-blue-500 p-12 text-center shadow-lg rounded-lg text-white;
            }
        }
    </style>
''')

with ui.row():
    ui.label('Hello').classes('blue-box')
    ui.label('world').classes('blue-box')

ui.run()
```

## ElementFilter

有时，搜索当前页面的 Python 元素树很方便。
`<tt>ElementFilter()</tt>` 允许通过元素类型、标记和内容进行强大的过滤。
它还提供了一个流畅的界面来应用更多过滤器，例如排除元素或过滤特定父元素内的元素。
该过滤器可以用作迭代器来迭代找到的元素，并且始终在迭代时应用，而不是在实例化时应用。

如果元素满足以下所有条件，则会生成该元素：

*   该元素属于指定的类型（如果指定）。
*   该元素不是任何排除的类型。
*   该元素具有所有指定的标记。
*   该元素没有任何排除的标记。
*   该元素包含所有指定的内容。
*   该元素不包含任何排除的内容。
*   它的祖先包括所有通过 `<tt>within</tt>` 定义的指定实例。
*   它的祖先不包括任何通过 `<tt>not_within</tt>` 定义的指定实例。
*   它的祖先包括所有通过 `<tt>within</tt>` 定义的指定类型。
*   它的祖先不包括任何通过 `<tt>not_within</tt>` 定义的指定类型。
*   它的祖先包括所有通过 `<tt>within</tt>` 定义的指定标记。
*   它的祖先不包括任何通过 `<tt>not_within</tt>` 定义的指定标记。

元素“内容”包括其文本、标签、图标、占位符、值、消息、内容、来源。
内容过滤的局部匹配（如“Hello”在“Hello World!”中）就足够了。

| 参数 | 描述 |
| --- | --- |
| kind: | 按元素类型过滤；迭代器将是 `<tt>kind</tt>` 类型 |
| marker: | 按元素标记过滤；可以是字符串列表或单个字符串，其中标记用空格分隔 |
| content: | 筛选在其内容属性（如 `<tt>.text</tt>`、`<tt>.value</tt>`、`<tt>.source</tt>`、...）之一中包含 `<tt>content</tt>` 的元素；可以是单个字符串或必须匹配的所有字符串的列表 |
| local_scope: | 如果为 `True`，则仅返回当前范围内的元素；默认情况下搜索整个页面（可以使用 `<tt>ElementFilter.DEFAULT_LOCAL_SCOPE = True</tt>` 更改此默认行为） |

```python
from nicegui import ElementFilter, ui

with ui.card():
    ui.button('button A')
    ui.label('label A')

with ui.card().mark('important'):
    ui.button('button B')
    ui.label('label B')

ElementFilter(kind=ui.label).within(marker='important').classes('text-xl')

ui.run()
```

请参阅 [更多...](element_filter)

## Query Selector

要操作诸如文档正文之类的元素，可以使用 `<cite>ui.query</cite>` 函数。
使用查询结果，您可以像使用其他 UI 元素一样添加类、样式和属性。
例如，这对于更改页面的背景颜色很有用（例如，`<cite>ui.query('body').classes('bg-green')</cite>`）。

| 参数 | 描述 |
| --- | --- |
| selector: | CSS 选择器（例如 "body"、"#my-id"、".my-class"、"div > p"） |

```python
from nicegui import ui

def set_background(color: str) -> None:
    ui.query('body').style(f'background-color: {color}')

ui.button('Blue', on_click=lambda: set_background('#ddeeff'))
ui.button('Orange', on_click=lambda: set_background('#ffeedd'))

ui.run()
```

请参阅 [更多...](query)

## Color Theming

设置 [Quasar](https://quasar.dev/style/theme-builder) 使用的主要颜色（primary、secondary、accent 等）。

| 参数 | 描述 |
| --- | --- |
| primary: | 主色（默认值：`#5898d4`） |
| secondary: | 次要颜色（默认值：`#26a69a`） |
| accent: |  强调色（默认值：`#9c27b0`） |
| dark: | 深色（默认值：`#1d1d1d`） |
| dark_page: | 深色页面颜色（默认值：`#121212`） |
| positive: | 正色（默认值：`#21ba45`） |
| negative: | 负色（默认值：`#c10015`） |
| info: | 信息颜色（默认值：`#31ccec`） |
| warning: | 警告颜色（默认值：`#f2c037`） |
| custom_colors: | 用于品牌化的自定义颜色定义（需要在首次使用自定义颜色之前调用 `<tt>ui.colors</tt>`） |

```python
from nicegui import ui

ui.button('Default', on_click=lambda: ui.colors())
ui.button('Gray', on_click=lambda: ui.colors(primary='#555'))

ui.run()
```

请参阅 [更多...](colors)

## CSS Variables

您可以通过设置 CSS 变量来自定义 NiceGUI 的外观。
目前，可以使用以下变量及其默认值：

*   `--nicegui-default-padding: 1rem`
*   `--nicegui-default-gap: 1rem`

```python
from nicegui import ui

ui.add_css('''
    :root {
        --nicegui-default-padding: 0.5rem;
        --nicegui-default-gap: 3rem;
    }
''')
with ui.card():
    ui.label('small padding')
    ui.label('large gap')

ui.run()
```

## Overwrite Tailwind's Default Style

Tailwind 重置 HTML 元素的默认样式，例如本示例中 `h2` 元素的字体大小。
您可以通过添加类型为 `text/tailwindcss` 的样式标签来覆盖这些默认值。
如果没有此类型，样式将过早求值，并被 Tailwind 覆盖。

```python
from nicegui import ui

ui.add_head_html('''
    <style type="text/tailwindcss">
        h2 {
            font-size: 150%;
        }
    </style>
''')
ui.html('<h2>Hello world!</h2>')

ui.run()
```

## Dark mode

您可以使用此元素在页面上启用、禁用或切换暗模式。
值 `<cite>None</cite>` 表示自动模式，该模式使用客户端的系统首选项。

请注意，此元素会覆盖 `<cite>ui.run</cite>` 函数和页面装饰器的 `<cite>dark</cite>` 参数。

| 参数 | 描述 |
| --- | --- |
| value: | 是否启用暗模式。如果为 None，则暗模式设置为自动。 |
| on_change: | 当值更改时调用的回调。 |

```python
from nicegui import ui

dark = ui.dark_mode()
ui.label('Switch mode:')
ui.button('Dark', on_click=dark.enable)
ui.button('Light', on_click=dark.disable)

ui.run()
```

请参阅 [更多...](dark_mode)

## Add CSS style definitions to the page

此函数可用于将 CSS 样式定义添加到 HTML 页面的头部。

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

请参阅 [更多...](add_style)
