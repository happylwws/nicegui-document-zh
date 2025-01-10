# *Audiovisual* Elements

## Image

Displays an image.
This element is based on Quasar's [QImg](https://quasar.dev/vue-components/img) component.

| 参数 | 描述 |
| --- | --- |
| source: |  图像的来源；可以是 URL、本地文件路径、base64 字符串或 PIL 图像 |

```python
from nicegui import ui

ui.image('https://picsum.photos/id/377/640/360')

ui.run()
```

See [more...](#image)

## Captions and Overlays

By nesting elements inside a `ui.image` you can create augmentations.

Use [Quasar classes](https://quasar.dev/vue-components/img) for positioning and styling captions.
To overlay an SVG, make the `viewBox` exactly the size of the image and provide `100%` width/height to match the actual rendered size.

```python
from nicegui import ui

with ui.image('https://picsum.photos/id/29/640/360'):
    ui.label('Nice!').classes('absolute-bottom text-subtitle2 text-center')

with ui.image('https://cdn.stocksnap.io/img-thumbs/960w/airplane-sky_DYPWDEEILG.jpg'):
    ui.html('''
        <svg viewBox="0 0 960 638" width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">
        <circle cx="445" cy="300" r="100" fill="none" stroke="red" stroke-width="10" />
        </svg>
    ''').classes('w-full bg-transparent')

ui.run()
```

## Interactive Image

Create an image with an SVG overlay that handles mouse events and yields image coordinates.
It is also the best choice for non-flickering image updates.
If the source URL changes faster than images can be loaded by the browser, some images are simply skipped.
Thereby repeatedly updating the image source will automatically adapt to the available bandwidth.
See [OpenCV Webcam](https://github.com/zauberzeug/nicegui/tree/main/examples/opencv_webcam/main.py) for an example.

The mouse event handler is called with mouse event arguments containing

*   `type` (the name of the JavaScript event),
*   `image_x` and `image_y` (image coordinates in pixels),
*   `button` and `buttons` (mouse button numbers from the JavaScript event), as well as
*   `alt`, `ctrl`, `meta`, and `shift` (modifier keys from the JavaScript event).

You can also pass a tuple of width and height instead of an image source.
This will create an empty image with the given size.

| 参数 | 描述 |
| --- | --- |
| source: |  图像的来源；可以是 URL、本地文件路径、base64 字符串或只是图像大小 |
| content: |  应该覆盖的 SVG 内容；视口具有与图像相同的尺寸 |
| size: | 图像的大小（宽度，高度），以像素为单位；仅当未设置 `source` 时使用 |
| on_mouse: |  鼠标事件的回调函数（包含图像坐标 `image_x` 和 `image_y`，以像素为单位） |
| events: | 要订阅的 JavaScript 事件列表（默认值：`['click']`） |
| cross: | 是否显示十字准星或颜色字符串（默认值：`False`） |

```python
from nicegui import events, ui

def mouse_handler(e: events.MouseEventArguments):
    color = 'SkyBlue' if e.type == 'mousedown' else 'SteelBlue'
    ii.content += f'<circle cx="{e.image_x}" cy="{e.image_y}" r="15" fill="none" stroke="{color}" stroke-width="4" />'
    ui.notify(f'{e.type} at ({e.image_x:.1f}, {e.image_y:.1f})')

src = 'https://picsum.photos/id/565/640/360'
ii = ui.interactive_image(src, on_mouse=mouse_handler, events=['mousedown', 'mouseup'], cross=True)

ui.run()
```

See [more...](#interactive_image)

## Audio

Displays an audio player.

| 参数 | 描述 |
| --- | --- |
| src: | 音频源的 URL 或本地文件路径 |
| controls: | 是否显示音频控件，如播放、暂停和音量（默认值：`True`） |
| autoplay: | 是否自动开始播放音频（默认值：`False`） |
| muted: | 音频是否应初始静音（默认值：`False`） |
| loop: | 音频是否应循环播放（默认值：`False`） |

See [here](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/audio#events)
for a list of events you can subscribe to using the generic event subscription `on()`.

```python
from nicegui import ui

a = ui.audio('https://cdn.pixabay.com/download/audio/2022/02/22/audio_d1718ab41b.mp3')
a.on('ended', lambda _: ui.notify('Audio playback completed'))

ui.button(on_click=lambda: a.props('muted'), icon='volume_off').props('outline')
ui.button(on_click=lambda: a.props(remove='muted'), icon='volume_up').props('outline')

ui.run()
```

See [more...](#audio)

## Video

Displays a video.

| 参数 | 描述 |
| --- | --- |
| src: | 视频源的 URL 或本地文件路径 |
| controls: | 是否显示视频控件，如播放、暂停和音量（默认值：`True`） |
| autoplay: | 是否自动开始播放视频（默认值：`False`） |
| muted: | 视频是否应初始静音（默认值：`False`） |
| loop: | 视频是否应循环播放（默认值：`False`） |

See [here](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#events)
for a list of events you can subscribe to using the generic event subscription `on()`.

```python
from nicegui import ui

v = ui.video('https://test-videos.co.uk/vids/bigbuckbunny/mp4/h264/360/Big_Buck_Bunny_360_10s_1MB.mp4')
v.on('ended', lambda _: ui.notify('Video playback completed'))

ui.run()
```

See [more...](#video)

## Icon

This element is based on Quasar's [QIcon](https://quasar.dev/vue-components/icon) component.

[Here](https://fonts.google.com/icons?icon.set=Material+Icons) is a reference of possible names.

| 参数 | 描述 |
| --- | --- |
| name: |  图标的名称（蛇形命名法，例如 `add_circle`） |
| size: | CSS 单位的大小，包括单位名称或标准大小名称（xs|sm|md|lg|xl），例如：16px, 2rem |
| color: | 图标颜色（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认值：`None`） |

```python
from nicegui import ui

ui.icon('thumb_up', color='primary').classes('text-5xl')

ui.run()
```

See [more...](#icon)

## Avatar

A avatar element wrapping Quasar's
[QAvatar](https://quasar.dev/vue-components/avatar) component.

| 参数 | 描述 |
| --- | --- |
| icon: |  图标的名称或带有 "img:" 前缀的图像路径（例如 "map", "img:path/to/image.png"） |
| color: |  背景颜色（可以是 Quasar、Tailwind 或 CSS 颜色，或者 `None`，默认值：`primary`） |
| text_color: |  来自 Quasar 颜色面板的颜色名称（例如 "primary", "teal-10"） |
| size: |  CSS 单位的大小，包括单位名称或标准大小名称（xs|sm|md|lg|xl）（例如 "16px", "2rem"） |
| font_size: | 内容（图标、文本）的大小，以 CSS 单位表示，包括单位名称（例如 "18px", "2rem"） |
| square: |  删除边框半径，使边框为方形（默认值：False） |
| rounded: |  为组件的方形形状应用小的标准边框半径（默认值：False） |

```python
from nicegui import ui

ui.avatar('favorite_border', text_color='grey-11', square=True)
ui.avatar('img:https://nicegui.io/logo_square.png', color='blue-2')

ui.run()
```

See [more...](#avatar)

## SVG

You can add Scalable Vector Graphics using the `ui.html` element.

```python
from nicegui import ui

content = '''
    <svg viewBox="0 0 200 200" width="100" height="100" xmlns="http://www.w3.org/2000/svg">
    <circle cx="100" cy="100" r="78" fill="#ffde34" stroke="black" stroke-width="3" />
    <circle cx="80" cy="85" r="8" />
    <circle cx="120" cy="85" r="8" />
    <path d="m60,120 C75,150 125,150 140,120" style="fill:none; stroke:black; stroke-width:8; stroke-linecap:round" />
    </svg>'''
ui.html(content)

ui.run()
```
