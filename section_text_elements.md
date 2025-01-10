# *Text* Elements

## Label

显示一些文本。

| 参数  | 描述 |
| --- | --- |
| text: | 标签的内容 |

```python
from nicegui import ui

ui.label('some label')

ui.run()
```

See [more...](label)

## Link

创建一个超链接。

要跳转到页面内的特定位置，可以使用 `ui.link_target("name")` 设置可链接的锚点，并使用 `ui.link(target="#name")` 链接到该锚点。

| 参数  | 描述 |
| --- | --- |
| text: | 显示文本 |
| target: | 页面函数，同一页面上的 NiceGUI 元素，或作为绝对 URL 或相对于基本 URL 的相对路径的字符串 |
| new_tab: | 在新标签页中打开链接 (默认: False) |

```python
from nicegui import ui

ui.link('NiceGUI on GitHub', 'https://github.com/zauberzeug/nicegui')

ui.run()
```

See [more...](link)

## Chat Message

基于 Quasar 的 [Chat Message](https://quasar.dev/vue-components/chat/) 组件。

| 参数  | 描述 |
| --- | --- |
| text: | 消息主体 (可以是多个消息部分的字符串列表) |
| name: | 消息作者的名称 |
| label: | 仅渲染标签标题/部分 |
| stamp: | 消息的时间戳 |
| avatar: | 头像的 URL |
| sent: | 渲染为已发送消息（即来自当前用户）(默认: False) |
| text_html: | 将文本渲染为 HTML (默认: False) |

```python
from nicegui import ui

ui.chat_message('Hello NiceGUI!',
                name='Robot',
                stamp='now',
                avatar='https://robohash.org/ui')

ui.run()
```

See [more...](chat_message)

## Generic Element

这个类是所有其他 UI 元素的基础类。但你可以使用它来创建具有任意 HTML 标签的元素。

| 参数  | 描述 |
| --- | --- |
| tag: | 元素的 HTML 标签 |
| _client: | 此元素的客户端 (仅供内部使用) |

```python
from nicegui import ui

with ui.element('div').classes('p-2 bg-blue-100'):
    ui.label('inside a colored div')

ui.run()
```

See [more...](element)

## Markdown Element

在页面上渲染 Markdown。

| 参数  | 描述 |
| --- | --- |
| content: | 要显示的 Markdown 内容 |
| extras: | [markdown2 扩展](https://github.com/trentm/python-markdown2/wiki/Extras#implemented-extras) 列表 (默认: `['fenced-code-blocks', 'tables']`) |

```python
from nicegui import ui

ui.markdown('This is **Markdown**.')

ui.run()
```

See [more...](markdown)

## ReStructuredText

在页面上渲染 ReStructuredText。

| 参数  | 描述 |
| --- | --- |
| content: | 要显示的 ReStructuredText 内容 |

```python
from nicegui import ui

ui.restructured_text('This is **reStructuredText**.')

ui.run()
```

See [more...](restructured_text)

## Mermaid Diagrams

渲染用受 Markdown 启发的 [Mermaid](https://mermaid.js.org/) 语言编写的图表和图表。
通过向 `ui.markdown` 元素提供扩展字符串 'mermaid'，mermaid 语法也可以在 Markdown 元素内部使用。

可选的配置字典在第一个图表渲染之前直接传递给 mermaid。
这可以用来设置诸如以下选项：

> `{'securityLevel': 'loose', ...}` - 允许在单击节点时运行 JavaScript
> `{'logLevel': 'info', ...}` - 将调试信息记录到控制台

有关选项的完整列表，请参阅 Mermaid 文档中的 `mermaid.initialize()` 方法。

| 参数  | 描述 |
| --- | --- |
| content: | 要显示的 Mermaid 内容 |
| config: | 要传递给 `mermaid.initialize()` 的配置字典 |

```python
from nicegui import ui

ui.mermaid('''
graph LR;
    A --> B;
    A --> C;
''')

ui.run()
```

See [more...](mermaid)

## HTML Element

在页面上渲染任意 HTML，并包裹在指定的标签中。
可以使用 [Tailwind](https://tailwindcss.com/) 进行样式设置。
你也可以使用 `ui.add_head_html` 将 html 代码添加到文档的 head 中，并使用 `ui.add_body_html` 将其添加到 body 中。

| 参数  | 描述 |
| --- | --- |
| content: | 要显示的 HTML 代码 |
| tag: | 包裹内容的 HTML 标签 (默认: "div") |

```python
from nicegui import ui

ui.html('This is <strong>HTML</strong>.')

ui.run()
```

See [more...](html)

## Other HTML Elements

有一个 `html` 模块，允许您插入其他 HTML 元素，如 `<span>`、`<div>`、`<p>` 等。
它等同于使用带有 `tag` 参数的 `ui.element` 方法。

与任何其他元素一样，你可以添加类、样式、属性、工具提示和事件。
一个便利之处在于，关键字参数会自动添加到元素的 `props` 字典中。

```python
from nicegui import html, ui

with html.section().style('font-size: 120%'):
    html.strong('This is bold.') \
        .classes('cursor-pointer') \
        .on('click', lambda: ui.notify('Bold!'))
    html.hr()
    html.em('This is italic.').tooltip('Nice!')
    with ui.row():
        html.img().props('src=https://placehold.co/60')
        html.img(src='https://placehold.co/60')

ui.run()
```
