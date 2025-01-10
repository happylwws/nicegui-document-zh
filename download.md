# ui.download

## Download

用于触发文件、URL 或字节下载的函数。

|  参数 | 描述 |
| --- | --- |
| src: |  要下载的目标 URL、文件的本地路径或原始数据 |
| filename: | 要下载的文件名 (默认: 服务器上的文件名) |
| media_type: | 要下载的文件的媒体类型 (默认: "") |

```python
from nicegui import ui

ui.button('Logo', on_click=lambda: ui.download('https://nicegui.io/logo.png'))

ui.run()
```

## Download raw bytes from memory

`download` 函数也可以用来下载内存中的原始字节。

```python
from nicegui import ui

ui.button('Download', on_click=lambda: ui.download(b'Hello World', 'hello.txt'))

ui.run()
```
