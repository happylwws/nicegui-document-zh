# Storage

## Storage

NiceGUI 提供了一个在你的应用程序中进行数据持久化的简单机制。它具有五种内置的存储类型：

- `app.storage.tab`:
  此字典存储在服务器端的内存中，对于每个非重复的选项卡会话是唯一的，并且可以保存任意对象。
  当重启服务器时数据将会丢失，直到 <a href="https://github.com/zauberzeug/nicegui/discussions/2841">https://github.com/zauberzeug/nicegui/discussions/2841</a> 被实现。
  此存储仅在 <a href="/documentation/page">页面构建器函数</a> 中可用，并且需要建立连接，可以通过 <a href="/documentation/page#wait_for_client_connection">`await client.connected()`</a> 获取。
- `app.storage.client`:
  此字典也存储在服务器端的内存中，对于每个客户端连接是唯一的，并且可以保存任意对象。
  当页面重新加载或用户导航到另一个页面时，数据将被丢弃。
  与存储在 `app.storage.tab` 中的数据可以在服务器上保留数天不同，
  `app.storage.client` 有助于缓存资源消耗大的对象，例如流媒体或数据库连接，你需要保持这些连接的活动状态，以便进行动态站点更新，但希望在用户离开页面或关闭浏览器时立即丢弃它们。
  此存储仅在 <a href="/documentation/page">页面构建器函数</a> 中可用。
- `app.storage.user`:
  每个字典都与浏览器会话 cookie 中保存的唯一标识符相关联，并存储在服务器端。
  此存储对于每个用户都是唯一的，并且可以在他们的所有浏览器选项卡中访问。
  `app.storage.browser['id']` 用于标识用户。
  此存储仅在 <a href="/documentation/page">页面构建器函数</a> 中可用，并且需要在 `ui.run()` 中使用 `storage_secret` 参数来签署浏览器会话 cookie。
- `app.storage.general`:
  此字典也存储在服务器端，它提供了一个所有用户都可以访问的共享存储空间。
- `app.storage.browser`:
  与前面的类型不同，此字典直接存储为浏览器会话 cookie，在同一用户的所有浏览器选项卡之间共享。
  但是，由于 `app.storage.user` 在减少数据负载、增强安全性和提供更大存储容量方面具有优势，因此通常是首选。
  默认情况下，NiceGUI 在 `app.storage.browser['id']` 中保存浏览器会话的唯一标识符。
  此存储仅在 <a href="/documentation/page">页面构建器函数</a> 中可用，并且需要在 `ui.run()` 中使用 `storage_secret` 参数来签署浏览器会话 cookie。

下表将帮助你选择存储类型。

| 存储类型 | `tab` | `client` | `user` | `general` | `browser` |
| --- | --- | --- | --- | --- | --- |
| 位置 | 服务器 | 服务器 | 服务器 | 服务器 | 浏览器 |
| 跨选项卡 | 否 | 否 | 是 | 是 | 是 |
| 跨浏览器 | 否 | 否 | 否 | 是 | 否 |
| 跨服务器重启 | 否 | 否 | 否 | 是 | 否 |
| 跨页面重载 | 是 | 否 | 是 | 是 | 是 |
| 需要页面构建器函数 | 是 | 是 | 是 | 否 | 是 |
| 需要客户端连接 | 是 | 否 | 否 | 否 | 否 |
| 仅在响应前写入 | 否 | 否 | 否 | 否 | 是 |
| 需要可序列化数据 | 否 | 否 | 是 | 是 | 是 |
| 需要 `storage_secret` | 否 | 否 | 是 | 否 | 是 |

```python
from nicegui import app, ui

@ui.page('/')
def index():
    app.storage.user['count'] = app.storage.user.get('count', 0) + 1
    with ui.row():
       ui.label('your own page visits:')
       ui.label().bind_text_from(app.storage.user, 'count')

ui.run(storage_secret='private key to secure the browser session cookie')
```

## Counting page visits

这里我们使用自动提供的浏览器存储的会话 ID 来计算唯一页面访问的次数。

```python
from collections import Counter
from datetime import datetime
from nicegui import app, ui

counter = Counter()
start = datetime.now().strftime('%H:%M, %d %B %Y')

@ui.page('/')
def index():
    counter[app.storage.browser['id']] += 1
    ui.label(f'{len(counter)} unique views ({sum(counter.values())} overall) since {start}')

ui.run(storage_secret='private key to secure the browser session cookie')
```

## Storing UI state

存储也可以与 <a href="/documentation/section_binding_properties">`bindings`</a> 结合使用。
这里我们在访问之间存储文本区域的值。
该笔记也在同一用户的所有选项卡之间共享。

```python
from nicegui import app, ui

@ui.page('/')
def index():
    ui.textarea('This note is kept between visits') \
        .classes('w-full').bind_value(app.storage.user, 'note')

ui.run()
```

## Storing data per browser tab

当在 `app.storage.tab` 中存储数据时，单个用户可以打开同一应用程序的多个选项卡，每个选项卡都有自己的存储数据。
这在某些场景下可能是有益的，例如搜索或执行数据分析时。
对于诸如登录银行账户或访问密码管理器之类的场景，使用这种易失性存储也更加安全。

```python
from nicegui import app, ui

@ui.page('/')
async def index():
    await ui.context.client.connected()
    app.storage.tab['count'] = app.storage.tab.get('count', 0) + 1
    ui.label(f'Tab reloaded {app.storage.tab["count"]} times')
    ui.button('Reload page', on_click=ui.navigate.reload)

ui.run()
```

## Short-term memory

`app.storage.client` 的目标是仅在当前页面访问期间存储数据。
与存储在 `app.storage.tab` 中的数据不同（只要选项卡保持打开状态，该数据就会在页面更改甚至浏览器重启之间保留），
如果用户关闭浏览器、重新加载页面或导航到另一个页面，`app.storage.client` 中的数据将被丢弃。
这对于资源消耗大、有意短期的或敏感的数据是有益的。
一个例子是数据库连接，它应该在用户离开页面后立即关闭。
此外，如果你希望每次用户重新加载时都返回具有默认设置的页面，则此存储也很有用。
同时，它在页面内导航期间保持数据的活动状态。
当以一定的时间间隔更新站点上的元素（例如实时提要）时，这也很有帮助。

```python
from nicegui import app, ui

@ui.page('/')
async def index():
    cache = app.storage.client
    cache['count'] = 0
    ui.label().bind_text_from(cache, 'count', lambda n: f'Updated {n} times')
    ui.button('Update content',
              on_click=lambda: cache.update(count=cache['count'] + 1))
    ui.button('Reload page', on_click=ui.navigate.reload)

ui.run()
```

## Indentation

默认情况下，通用和用户存储数据以 JSON 格式存储，不进行缩进。
你可以通过设置 `app.storage.general.indent = True` 或 `app.storage.user.indent = True` 将其更改为 2 个空格的缩进。
