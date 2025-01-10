# ui.run_javascript

## Run JavaScript

此函数在浏览器中执行页面上的任意 JavaScript 代码。

在调用此函数之前，客户端必须已连接。

要通过 ID 访问客户端的 Vue 组件或 HTML 元素，请使用 JavaScript 函数 `getElement()` 或 `getHtmlElement()`。

如果该函数被 await，则返回 JavaScript 代码的结果。否则，JavaScript 代码将执行，而不等待响应。

请注意，仅页面函数支持从客户端请求数据，而不支持共享的自动索引页面。

| 参数    | 描述                                                         |
| :------ | :----------------------------------------------------------- |
| code:    | 要运行的 JavaScript 代码                                       |
| timeout: | 超时时间（以秒为单位）（默认值：`1.0`）                         |
| return:  | 可等待的 AwaitableResponse，可以等待以获取 JavaScript 代码的结果 |

```python
from nicegui import ui

@ui.page('/')
def page():
    def alert():
        ui.run_javascript('alert("Hello!")')

    async def get_date():
        time = await ui.run_javascript('Date()')
        ui.notify(f'Browser time: {time}')

    def access_elements():
        ui.run_javascript(f'getHtmlElement({label.id}).innerText += " Hello!"')

    ui.button('fire and forget', on_click=alert)
    ui.button('receive result', on_click=get_date)
    ui.button('access elements', on_click=access_elements)
    label = ui.label()

ui.run()
```

## Run async JavaScript

使用 `run_javascript`，您还可以在浏览器中运行异步代码。以下演示显示如何获取用户的当前位置。

```python
from nicegui import ui

@ui.page('/')
def page():
    async def show_location():
        response = await ui.run_javascript('''
            return await new Promise((resolve, reject) => {
                if (!navigator.geolocation) {
                    reject(new Error('Geolocation is not supported by your browser'));
                } else {
                    navigator.geolocation.getCurrentPosition(
                        (position) => {
                            resolve({
                                latitude: position.coords.latitude,
                                longitude: position.coords.longitude,
                            });
                        },
                        () => {
                            reject(new Error('Unable to retrieve your location'));
                        }
                    );
                }
            });
        ''', timeout=5.0)
        ui.notify(f'Your location is {response["latitude"]}, {response["longitude"]}')

    ui.button('Show location', on_click=show_location)

ui.run()
```
