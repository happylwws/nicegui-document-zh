# *Controls*

## Button

这是一个基于 Quasar 的 [QBtn](https://quasar.dev/vue-components/button) 组件的元素。

`color` 参数接受 Quasar 颜色、Tailwind 颜色或 CSS 颜色。
如果使用 Quasar 颜色，按钮将根据 Quasar 主题进行样式设置，包括文本颜色。
请注意，有些颜色如 “red” 既是 Quasar 颜色又是 CSS 颜色。
在这种情况下，将使用 Quasar 颜色。

| 参数 | 描述 |
| --- | --- |
| text: | 按钮的标签 |
| on_click: | 当按下按钮时调用的回调函数 |
| color: | 按钮的颜色（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认为 'primary'） |
| icon: | 要在按钮上显示的图标名称（默认为 `None`） |

```python
from nicegui import ui

ui.button('Click me!', on_click=lambda: ui.notify('You clicked me!'))

ui.run()
```

See [more...](button)

## Button Group

这个元素基于 Quasar 的 [QBtnGroup](https://quasar.dev/vue-components/button-group) 组件。
你必须在父按钮组和子按钮上使用相同的设计属性。

```python
from nicegui import ui

with ui.button_group():
    ui.button('One', on_click=lambda: ui.notify('You clicked Button 1!'))
    ui.button('Two', on_click=lambda: ui.notify('You clicked Button 2!'))
    ui.button('Three', on_click=lambda: ui.notify('You clicked Button 3!'))

ui.run()
```

See [more...](button_group)

## Dropdown Button

这个元素基于 Quasar 的 [QBtnDropDown](https://quasar.dev/vue-components/button-dropdown) 组件。

`color` 参数接受 Quasar 颜色、Tailwind 颜色或 CSS 颜色。
如果使用 Quasar 颜色，按钮将根据 Quasar 主题进行样式设置，包括文本颜色。
请注意，有些颜色如 “red” 既是 Quasar 颜色又是 CSS 颜色。
在这种情况下，将使用 Quasar 颜色。

| 参数 | 描述 |
| --- | --- |
| text: | 按钮的标签 |
| value: | 下拉菜单是否打开（默认为 `False`） |
| on_value_change: | 当下拉菜单打开或关闭时调用的回调函数 |
| on_click: | 当按下按钮时调用的回调函数 |
| color: | 按钮的颜色（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认为 'primary'） |
| icon: | 要在按钮上显示的图标名称（默认为 `None`） |
| auto_close: | 当点击项目时，下拉菜单是否应自动关闭（默认为 `False`） |
| split: | 是否将下拉图标拆分为单独的按钮（默认为 `False`） |

```python
from nicegui import ui

with ui.dropdown_button('Open me!', auto_close=True):
    ui.item('Item 1', on_click=lambda: ui.notify('You clicked item 1'))
    ui.item('Item 2', on_click=lambda: ui.notify('You clicked item 2'))

ui.run()
```

See [more...](button_dropdown)

## Badge

一个包装了 Quasar 的 [QBadge](https://quasar.dev/vue-components/badge) 组件的徽章元素。

| 参数 | 描述 |
| --- | --- |
| text: | 文本字段的初始值 |
| color: | 组件的颜色名称（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认为 "primary"） |
| text_color: | 文本颜色（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认为 `None`） |
| outline: | 使用 'outline' 设计（仅着色文本和边框）（默认为 False） |

```python
from nicegui import ui

with ui.button('Click me!', on_click=lambda: badge.set_text(int(badge.text) + 1)):
    badge = ui.badge('0', color='red').props('floating')

ui.run()
```

See [more...](badge)

## Chip

一个包装了 Quasar 的 [QChip](https://quasar.dev/vue-components/chip) 组件的 Chip 元素。它可以是可点击、可选择和可移除的。

| 参数 | 描述 |
| --- | --- |
| text: | 文本字段的初始值 (默认为 "") |
| icon: | 要在 Chip 上显示的图标名称 (默认为 `None`) |
| color: | 组件的颜色名称（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认为 "primary"） |
| text_color: | 文本颜色（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认为 `None`） |
| on_click: | 当 Chip 被点击时调用的回调函数。如果设置了，则使 Chip 可点击 |
| selectable: | Chip 是否可选择 (默认为 `False`) |
| selected: | Chip 是否被选中 (默认为 `False`) |
| on_selection_change: | 当 Chip 的选择状态更改时调用的回调函数 |
| removable: | Chip 是否可移除。如果为 True，则显示一个小 "x" 按钮 (默认为 `False`) |
| on_value_change: | 当 Chip 被移除或取消移除时调用的回调函数 |

```python
from nicegui import ui

with ui.row().classes('gap-1'):
    ui.chip('Click me', icon='ads_click', on_click=lambda: ui.notify('Clicked'))
    ui.chip('Selectable', selectable=True, icon='bookmark', color='orange')
    ui.chip('Removable', removable=True, icon='label', color='indigo-3')
    ui.chip('Styled', icon='star', color='green').props('outline square')
    ui.chip('Disabled', icon='block', color='red').set_enabled(False)

ui.run()
```

See [more...](chip)

## Toggle

这个元素基于 Quasar 的 [QBtnToggle](https://quasar.dev/vue-components/button-toggle) 组件。

选项可以指定为值列表，或将值映射到标签的字典。
在操作选项后，调用 `update()` 以更新 UI 中的选项。

| 参数 | 描述 |
| --- | --- |
| options: | 指定选项的列表 ['value1', ...] 或字典 {'value1':'label1', ...} |
| value: | 初始值 |
| on_change: | 当选择更改时执行的回调函数 |
| clearable: | 是否可以通过点击选定的选项来清除切换 |

```python
from nicegui import ui

toggle1 = ui.toggle([1, 2, 3], value=1)
toggle2 = ui.toggle({1: 'A', 2: 'B', 3: 'C'}).bind_value(toggle1, 'value')

ui.run()
```

See [more...](toggle)

## Radio Selection

这个元素基于 Quasar 的 [QRadio](https://quasar.dev/vue-components/radio) 组件。

选项可以指定为值列表，或将值映射到标签的字典。
在操作选项后，调用 `update()` 以更新 UI 中的选项。

| 参数 | 描述 |
| --- | --- |
| options: | 指定选项的列表 ['value1', ...] 或字典 {'value1':'label1', ...} |
| value: | 初始值 |
| on_change: | 当选择更改时执行的回调函数 |

```python
from nicegui import ui

radio1 = ui.radio([1, 2, 3], value=1).props('inline')
radio2 = ui.radio({1: 'A', 2: 'B', 3: 'C'}).props('inline').bind_value(radio1, 'value')

ui.run()
```

See [more...](radio)

## Dropdown Selection

这个元素基于 Quasar 的 [QSelect](https://quasar.dev/vue-components/select) 组件。

选项可以指定为值列表，或将值映射到标签的字典。
在操作选项后，调用 `update()` 以更新 UI 中的选项。

如果 `with_input` 为 True，则会显示一个输入字段来过滤选项。

如果 `new_value_mode` 不为 None，则表示 `with_input=True`，并且用户可以在输入字段中输入新值。
有关详细信息，请参阅 [Quasar 的文档](https://quasar.dev/vue-components/select#the-new-value-mode-prop)。
请注意，当以编程方式设置 `value` 属性时，此模式无效。

您可以使用 `validation` 参数来定义验证规则字典，例如 `{'Too long!': lambda value: len(value) < 3}`。
第一个失败的规则的键将显示为错误消息。
或者，您可以传递一个返回可选错误消息的可调用对象。
要禁用每次值更改时的自动验证，您可以使用 `without_auto_validation` 方法。

| 参数 | 描述 |
| --- | --- |
| options: | 指定选项的列表 ['value1', ...] 或字典 {'value1':'label1', ...} |
| label: | 要在选择上方显示的标签 |
| value: | 初始值 |
| on_change: | 当选择更改时执行的回调函数 |
| with_input: | 是否显示输入字段来过滤选项 |
| new_value_mode: | 处理来自用户输入的新值（默认为 None，即不接受新值） |
| multiple: | 是否允许多选 |
| clearable: | 是否添加一个按钮来清除选择 |
| validation: | 验证规则字典或返回可选错误消息的可调用对象 (默认为 None，不进行验证) |
| key_generator: | 用于为新值生成字典键的回调或迭代器 |

```python
from nicegui import ui

select1 = ui.select([1, 2, 3], value=1)
select2 = ui.select({1: 'One', 2: 'Two', 3: 'Three'}).bind_value(select1, 'value')

ui.run()
```

See [more...](select)

## Checkbox

这个元素基于 Quasar 的 [QCheckbox](https://quasar.dev/vue-components/checkbox) 组件。

| 参数 | 描述 |
| --- | --- |
| text: | 要在复选框旁边显示的标签 |
| value: | 是否应在初始时被选中（默认为 `False`） |
| on_change: | 当值更改时执行的回调函数 |

```python
from nicegui import ui

checkbox = ui.checkbox('check me')
ui.label('Check!').bind_visibility_from(checkbox, 'value')

ui.run()
```

See [more...](checkbox)

## Switch

这个元素基于 Quasar 的 [QToggle](https://quasar.dev/vue-components/toggle) 组件。

| 参数 | 描述 |
| --- | --- |
| text: | 要在开关旁边显示的标签 |
| value: | 是否应在初始时激活（默认为 `False`） |
| on_change: | 当用户更改状态时调用的回调函数 |

```python
from nicegui import ui

switch = ui.switch('switch me')
ui.label('Switch!').bind_visibility_from(switch, 'value')

ui.run()
```

See [more...](switch)

## Slider

这个元素基于 Quasar 的 [QSlider](https://quasar.dev/vue-components/slider) 组件。

| 参数 | 描述 |
| --- | --- |
| min: | 滑块的下限 |
| max: | 滑块的上限 |
| step: | 步长 |
| value: | 设置滑块初始位置的初始值 |
| on_change: | 当用户释放滑块时调用的回调函数 |

```python
from nicegui import ui

slider = ui.slider(min=0, max=100, value=50)
ui.label().bind_text_from(slider, 'value')

ui.run()
```

See [more...](slider)

## Range

这个元素基于 Quasar 的 [QRange](https://quasar.dev/vue-components/range) 组件。

| 参数 | 描述 |
| --- | --- |
| min: | 范围的下限 |
| max: | 范围的上限 |
| step: | 步长 |
| value: | 设置范围的最小和最大位置的初始值 |
| on_change: | 当用户释放范围时调用的回调函数 |

```python
from nicegui import ui

min_max_range = ui.range(min=0, max=100, value={'min': 20, 'max': 80})
ui.label().bind_text_from(min_max_range, 'value',
                          backward=lambda v: f'min: {v["min"]}, max: {v["max"]}')

ui.run()
```

See [more...](range)

## Joystick

创建一个基于 [nipple.js](https://yoannmoi.net/nipplejs/) 的摇杆。

| 参数 | 描述 |
| --- | --- |
| on_start: | 当用户触摸摇杆时执行的回调函数 |
| on_move: | 当用户移动摇杆时执行的回调函数 |
| on_end: | 当用户释放摇杆时执行的回调函数 |
| throttle: | 移动事件的节流间隔（以秒为单位）（默认为 0.05） |
| options: | 应传递给 [底层 nipple.js 库](https://github.com/yoannmoinet/nipplejs#options) 的参数，如 `color` |

```python
from nicegui import ui

ui.joystick(
    color='blue', size=50,
    on_move=lambda e: coordinates.set_text(f'{e.x:.3f}, {e.y:.3f}'),
    on_end=lambda _: coordinates.set_text('0, 0'),
).classes('bg-slate-300')
coordinates = ui.label('0, 0')

ui.run()
```

See [more...](joystick)

## Text Input

这个元素基于 Quasar 的 [QInput](https://quasar.dev/vue-components/input) 组件。

`on_change` 事件在每次按键时调用，并且值会相应更新。
如果您想等到用户确认输入，您可以注册一个自定义事件回调，例如
`ui.input(...).on('keydown.enter', ...)` 或 `ui.input(...).on('blur', ...)`。

您可以使用 `validation` 参数来定义验证规则字典，例如 `{'Too long!': lambda value: len(value) < 3}`。
第一个失败的规则的键将显示为错误消息。
或者，您可以传递一个返回可选错误消息的可调用对象。
要禁用每次值更改时的自动验证，您可以使用 `without_auto_validation` 方法。

关于样式输入的注意事项：
Quasar 的 `QInput` 组件是原生 `input` 元素的包装器。
这意味着您无法直接设置输入的样式，
但是您可以使用 `input-class` 和 `input-style` 属性来设置原生输入元素的样式。
有关更多详细信息，请参阅 [QInput](https://quasar.dev/vue-components/input) 文档中的“Style”属性部分。

| 参数 | 描述 |
| --- | --- |
| label: | 文本输入的显示标签 |
| placeholder: | 如果未输入值，则显示的文本 |
| value: | 文本输入的当前值 |
| password: | 是否隐藏输入（默认为 False） |
| password_toggle_button: | 是否显示一个按钮来切换密码可见性（默认为 False） |
| on_change: | 当值更改时执行的回调函数 |
| autocomplete: | 用于自动完成的可选字符串列表 |
| validation: | 验证规则字典或返回可选错误消息的可调用对象（默认为 None，不进行验证） |

```python
from nicegui import ui

ui.input(label='Text', placeholder='start typing',
         on_change=lambda e: result.set_text('you typed: ' + e.value),
         validation={'Input too long': lambda value: len(value) < 20})
result = ui.label()

ui.run()
```

See [more...](input)

## Textarea

这个元素基于 Quasar 的 [QInput](https://quasar.dev/vue-components/input) 组件。
`type` 被设置为 `textarea` 以创建多行文本输入。

您可以使用 `validation` 参数来定义验证规则字典，例如 `{'Too long!': lambda value: len(value) < 3}`。
第一个失败的规则的键将显示为错误消息。
或者，您可以传递一个返回可选错误消息的可调用对象。
要禁用每次值更改时的自动验证，您可以使用 `without_auto_validation` 方法。

| 参数 | 描述 |
| --- | --- |
| label: | 文本区域的显示名称 |
| placeholder: | 如果未输入值，则显示的文本 |
| value: | 字段的初始值 |
| on_change: | 当值更改时执行的回调函数 |
| validation: | 验证规则字典或返回可选错误消息的可调用对象（默认为 None，不进行验证） |

```python
from nicegui import ui

ui.textarea(label='Text', placeholder='start typing',
            on_change=lambda e: result.set_text('you typed: ' + e.value))
result = ui.label()

ui.run()
```

See [more...](textarea)

## CodeMirror

使用 [CodeMirror](https://codemirror.net/) 创建代码编辑器的元素。

它支持超过 140 种语言的语法高亮显示、超过 30 种主题、行号、代码折叠、（有限的）自动完成等等。

支持的语言和主题：

*   语言：支持的语言列表可以在 [@codemirror/language-data](https://github.com/codemirror/language-data/blob/main/src/language-data.ts) 包中找到。
*   主题：可以在 [@uiw/codemirror-themes-all](https://github.com/uiwjs/react-codemirror/tree/master/themes/all) 包中找到列表。

在运行时，可以使用 `supported_languages` 和 `supported_themes` 方法来获取支持的语言和主题。

| 参数 | 描述 |
| --- | --- |
| value: | 编辑器的初始值（默认为 ""） |
| on_change: | 当值更改时要执行的回调函数（默认为 `None`） |
| language: | 编辑器的初始语言（不区分大小写，默认为 `None`） |
| theme: | 编辑器的初始主题（默认为 "basicLight"） |
| indent: | 用于缩进的字符串（任何完全由相同的空格字符组成的字符串，默认为 "    "） |
| line_wrapping: | 是否换行（默认为 `False`） |
| highlight_whitespace: | 是否高亮显示空格（默认为 `False`） |

```python
from nicegui import ui

editor = ui.codemirror('print("Edit me!")', language='Python').classes('h-32')
ui.select(editor.supported_languages, label='Language', clearable=True) \
    .classes('w-32').bind_value(editor, 'language')
ui.select(editor.supported_themes, label='Theme') \
    .classes('w-32').bind_value(editor, 'theme')

ui.run()
```

See [more...](codemirror)

## Number Input

这个元素基于 Quasar 的 [QInput](https://quasar.dev/vue-components/input) 组件。

您可以使用 `validation` 参数来定义验证规则字典，例如 `{'Too small!': lambda value: value > 3}`。
第一个失败的规则的键将显示为错误消息。
或者，您可以传递一个返回可选错误消息的可调用对象。
要禁用每次值更改时的自动验证，您可以使用 `without_auto_validation` 方法。

| 参数 | 描述 |
| --- | --- |
| label: | 数字输入的显示名称 |
| placeholder: | 如果未输入值，则显示的文本 |
| value: | 字段的初始值 |
| min: | 允许的最小值 |
| max: | 允许的最大值 |
| precision: | 允许的小数位数（默认为无限制，负数：小数点前的位数） |
| step: | 步进按钮的步长 |
| prefix: | 要添加到显示值前面的前缀 |
| suffix: | 要附加到显示值后面的后缀 |
| format: | 一个像 "%.2f" 这样的字符串来格式化显示值 |
| on_change: | 当值更改时执行的回调函数 |
| validation: | 验证规则字典或返回可选错误消息的可调用对象（默认为 None，不进行验证） |

```python
from nicegui import ui

ui.number(label='Number', value=3.1415927, format='%.2f',
          on_change=lambda e: result.set_text(f'you entered: {e.value}'))
result = ui.label()

ui.run()
```

See [more...](number)

## Knob

这个元素基于 Quasar 的 [QKnob](https://quasar.dev/vue-components/knob) 组件。
该元素用于通过鼠标/触摸平移从用户处获取数字输入。

| 参数 | 描述 |
| --- | --- |
| value: | 初始值（默认为 0.0） |
| min: | 最小值（默认为 0.0） |
| max: | 最大值（默认为 1.0） |
| step: | 步长（默认为 0.01） |
| color: | 旋钮颜色（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认为 "primary"） |
| center_color: | 组件中心部分的颜色名称，例如：primary，teal-10 |
| track_color: | 组件轨道的颜色名称，例如：primary，teal-10 |
| size: | CSS 单位的大小，包括单位名称或标准大小名称（xs|sm|md|lg|xl），例如：16px，2rem |
| show_value: | 是否以文本形式显示值 |
| on_change: | 当值更改时执行的回调函数 |

```python
from nicegui import ui

knob = ui.knob(0.3, show_value=True)

with ui.knob(color='orange', track_color='grey-2').bind_value(knob, 'value'):
    ui.icon('volume_up')

ui.run()
```

See [more...](knob)

## Color Input

此元素使用颜色选择器扩展了 Quasar 的 [QInput](https://quasar.dev/vue-components/input) 组件。

| 参数 | 描述 |
| --- | --- |
| label: | 颜色输入的显示标签 |
| placeholder: | 如果未选择颜色，则显示的文本 |
| value: | 当前颜色值 |
| on_change: | 当值更改时执行的回调函数 |
| preview: | 将按钮背景更改为所选颜色（默认为 False） |

```python
from nicegui import ui

label = ui.label('Change my color!')
ui.color_input(label='Color', value='#000000',
               on_change=lambda e: label.style(f'color:{e.value}'))

ui.run()
```

See [more...](color_input)

## Color Picker

这个元素基于 Quasar 的 [QMenu](https://quasar.dev/vue-components/menu) 和 [QColor](https://quasar.dev/vue-components/color-picker) 组件。

| 参数 | 描述 |
| --- | --- |
| on_pick: | 当选择颜色时执行的回调函数 |
| value: | 菜单是否已打开（默认为 `False`） |

```python
from nicegui import ui

with ui.button(icon='colorize') as button:
    ui.color_picker(on_pick=lambda e: button.classes(f'!bg-[{e.color}]'))

ui.run()
```

See [more...](color_picker)

## Date Input

这个元素基于 Quasar 的 [QDate](https://quasar.dev/vue-components/date) 组件。
日期是 `mask` 参数定义的格式字符串。

您还可以使用 `range` 或 `multiple` 属性选择日期范围或多个日期：

```
ui.date({'from': '2023-01-01', 'to': '2023-01-05'}).props('range')
ui.date(['2023-01-01', '2023-01-02', '2023-01-03']).props('multiple')
ui.date([{'from': '2023-01-01', 'to': '2023-01-05'}, '2023-01-07']).props('multiple range')
```

| 参数 | 描述 |
| --- | --- |
| value: | 初始日期 |
| mask: | 日期字符串的格式（默认为 'YYYY-MM-DD'） |
| on_change: | 当更改日期时执行的回调函数 |

```python
from nicegui import ui

ui.date(value='2023-01-01', on_change=lambda e: result.set_text(e.value))
result = ui.label()

ui.run()
```

See [more...](date)

## Time Input

这个元素基于 Quasar 的 [QTime](https://quasar.dev/vue-components/time) 组件。
时间是 `mask` 参数定义的格式字符串。

| 参数 | 描述 |
| --- | --- |
| value: | 初始时间 |
| mask: | 时间字符串的格式（默认为 'HH:mm'） |
| on_change: | 当更改时间时执行的回调函数 |

```python
from nicegui import ui

ui.time(value='12:00', on_change=lambda e: result.set_text(e.value))
result = ui.label()

ui.run()
```

See [more...](time)

## File Upload

基于 Quasar 的 [QUploader](https://quasar.dev/vue-components/uploader) 组件。

| 参数 | 描述 |
| --- | --- |
| multiple: | 允许一次上传多个文件（默认为 `False`） |
| max_file_size: | 最大文件大小（以字节为单位）（默认为 `0`） |
| max_total_size: | 所有文件的最大总大小（以字节为单位）（默认为 `0`） |
| max_files: | 最大文件数（默认为 `0`） |
| on_upload: | 为每个上传的文件执行的回调函数 |
| on_multi_upload: | 在上传多个文件后执行的回调函数 |
| on_rejected: | 为每个被拒绝的文件执行的回调函数 |
| label: | 上传器的标签（默认为 `''`） |
| auto_upload: | 当选择文件时自动上传文件（默认为 `False`） |

```python
from nicegui import ui

ui.upload(on_upload=lambda e: ui.notify(f'Uploaded {e.name}')).classes('max-w-full')

ui.run()
```

See [more...](upload)
