# ElementFilter

## ElementFilter

有时，搜索当前页面的 Python 元素树会很方便。`ElementFilter()` 允许通过元素类型、标记和内容进行强大的过滤。它还提供了一个流畅的接口来应用更多过滤器，例如排除元素或过滤特定父元素内的元素。该过滤器可以用作迭代器来迭代找到的元素，并且始终在迭代时应用，而不是在实例化时应用。

如果元素匹配以下所有条件，则会生成该元素：

*   该元素属于指定的类型（如果已指定）。
*   该元素不是任何排除的类型。
*   该元素具有所有指定的标记。
*   该元素不具有任何排除的标记。
*   该元素包含所有指定的内容。
*   该元素不包含任何排除的内容。
*   其祖先包括通过 `within` 定义的所有指定实例。
*   其祖先不包括通过 `not_within` 定义的任何指定实例。
*   其祖先包括通过 `within` 定义的所有指定类型。
*   其祖先不包括通过 `not_within` 定义的任何指定类型。
*   其祖先包括通过 `within` 定义的所有指定标记。
*   其祖先不包括通过 `not_within` 定义的任何指定标记。

元素“内容”包括其文本、标签、图标、占位符、值、消息、内容、源。部分匹配（如“Hello”在“Hello World!”中）对于内容过滤就足够了。

| 参数          | 描述                                                                                                                                                                                                                                                        |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| kind:       | 按元素类型过滤；迭代器将为 `kind` 类型                                                                                                                                                                                                                          |
| marker:     | 按元素标记过滤；可以是字符串列表或单个字符串，其中标记用空格分隔                                                                                                                                                                                                                                 |
| content:    | 过滤包含 `content` 在其内容属性（如 `.text`、`.value`、`.source` 等）之一中的元素；可以是单个字符串或必须全部匹配的字符串列表                                                                                                                                                                                                             |
| local_scope: | 如果为 `True`，则仅返回当前作用域内的元素；默认情况下搜索整个页面（可以使用 `ElementFilter.DEFAULT_LOCAL_SCOPE = True` 更改此默认行为）                                                                                                                                                                                               |

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

## Find all elements with text property

`text` 属性由一个名为 `TextElement` 的 mixin 提供。如果我们按这样的 mixin 进行过滤，ElementFilter 本身将提供一个类型化的可迭代对象。

```python
from nicegui import ElementFilter, ui
from nicegui.elements.mixins.text_element import TextElement

with ui.card():
    ui.button('button')
    ui.icon('home')
    ui.label('label A')
    ui.label('label B')
    ui.html('HTML')

ui.label(', '.join(b.text for b in ElementFilter(kind=TextElement)))

ui.run()
```

## Markers

标记是使用字符串标记元素的简单方法，可以通过 `ElementFilter` 查询。

```python
from nicegui import ElementFilter, ui

with ui.card().mark('red'):
    ui.label('label A')
with ui.card().mark('strong'):
    ui.label('label B')
with ui.card().mark('red strong'):
    ui.label('label C')

ElementFilter(marker='red').classes('bg-red-200')
ElementFilter(marker='strong').classes('text-bold')
ElementFilter(marker='red strong').classes('bg-red-600 text-white')

ui.run()
```

## Find elements on other pages

您可以使用 `app.clients` 迭代器将元素过滤器应用于特定页面的所有客户端。

```python
import time
from nicegui import app, ui

@ui.page('/log')
def page():
    ui.log()

def log_time():
    for client in app.clients('/log'):
        with client:
            for log in ElementFilter(kind=ui.log):
                log.push(f'{time.strftime("%H:%M:%S")}')

ui.button('Log current time', on_click=log_time)
ui.link('Open log', '/log', new_tab=True)

ui.run()
```

## Reference

### Initializer

| 参数          | 描述                                                                                                                                                                                                                                                        |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| kind:       | 按元素类型过滤；迭代器将为 `kind` 类型                                                                                                                                                                                                                          |
| marker:     | 按元素标记过滤；可以是字符串列表或单个字符串，其中标记用空格分隔                                                                                                                                                                                                                                 |
| content:    | 过滤包含 `content` 在其内容属性（如 `.text`、`.value`、`.source` 等）之一中的元素；可以是单个字符串或必须全部匹配的字符串列表                                                                                                                                                                                                             |
| local_scope: | 如果为 `True`，则仅返回当前作用域内的元素；默认情况下搜索整个页面（可以使用 `ElementFilter.DEFAULT_LOCAL_SCOPE = True` 更改此默认行为）                                                                                                                                                                                               |

### Methods

**`classes`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> Self`

应用、移除或替换 HTML 类。

这允许使用 [Tailwind](https://tailwindcss.com/) 或 [Quasar](https://quasar.dev/) 类修改元素的外观或布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。

|  参数    | 描述                                    |
| ------- | --------------------------------------- |
| add:    | 以空格分隔的类字符串                               |
| remove: | 要从元素中删除的以空格分隔的类字符串                        |
| replace: | 用于替换现有类的以空格分隔的类字符串                        |

**`exclude`**`(kind: Optional[Type[Element]] = None, marker: Optional[str] = None, content: Optional[str] = None) -> Self`

排除具有特定元素类型、标记或内容的元素。

**`not_within`**`(kind: Optional[Type[Element]] = None, marker: Optional[str] = None, instance: Union[Element, List[Element], None] = None) -> Self`

排除具有特定类型或标记的父元素的元素。

**`props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> Self`

添加或删除属性。

这允许使用 [Quasar](https://quasar.dev/) 属性修改元素的外观或布局。
由于属性只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。

如果未指定值，则布尔属性假定为 `True`。

|  参数    | 描述                                   |
| ------- | -------------------------------------- |
| add:    | 要添加的布尔值或键值对的空格分隔列表                    |
| remove: | 要删除的属性键的空格分隔列表                            |

**`style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> Self`

应用、移除或替换 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。

|  参数    | 描述                                |
| ------- | ---------------------------------- |
| add:    | 要添加到元素的以分号分隔的样式列表                  |
| remove: | 要从元素中删除的以分号分隔的样式列表                |
| replace: | 用于替换现有样式的以分号分隔的样式列表              |

**`within`**`(kind: Optional[Type[Element]] = None, marker: Optional[str] = None, instance: Union[Element, List[Element], None] = None) -> Self`

过滤在父层次结构中具有特定匹配项的元素。
