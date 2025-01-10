# ui.keyboard

## Keyboard

添加全局键盘事件跟踪。

<tt class="docutils literal">on_key</tt> 回调接收一个 <tt class="docutils literal">KeyEventArguments</tt> 对象，该对象具有以下属性：

*   <tt class="docutils literal">sender</tt>: <tt class="docutils literal">Keyboard</tt> 元素
*   <tt class="docutils literal">client</tt>: 客户端对象
*   <dl class="first docutils">
    <dt><tt class="docutils literal">action</tt>: 一个 <tt class="docutils literal">KeyboardAction</tt> 对象，具有以下属性:</dt>
    <dd><ul class="first last">
    <li><tt class="docutils literal">keydown</tt>: 是否按下键</li>
    <li><tt class="docutils literal">keyup</tt>: 是否释放键</li>
    <li><tt class="docutils literal">repeat</tt>: 键盘事件是否是重复的</li>
    </ul>
    </dd>
    </dl>
*   <dl class="first docutils">
    <dt><tt class="docutils literal">key</tt>: 一个 <tt class="docutils literal">KeyboardKey</tt> 对象，具有以下属性:</dt>
    <dd><ul class="first last">
    <li><tt class="docutils literal">name</tt>: 键的名称 (例如 "a", "Enter", "ArrowLeft"；有关可能值的列表，请参阅 <a class="reference external" href="https://developer.mozilla.org/en-US/docs/Web/API/UI_Events/Keyboard_event_key_values">此处</a>)</li>
    <li><tt class="docutils literal">code</tt>: 键的代码 (例如 "KeyA", "Enter", "ArrowLeft")</li>
    <li><tt class="docutils literal">location</tt>: 键的位置 (标准键为 0，左侧键为 1，右侧键为 2，数字键盘键为 3)</li>
    </ul>
    </dd>
    </dl>
*   <dl class="first docutils">
    <dt><tt class="docutils literal">modifiers</tt>: 一个 <tt class="docutils literal">KeyboardModifiers</tt> 对象，具有以下属性:</dt>
    <dd><ul class="first last">
    <li><tt class="docutils literal">alt</tt>: 是否按下了 alt 键</li>
    <li><tt class="docutils literal">ctrl</tt>: 是否按下了 ctrl 键</li>
    <li><tt class="docutils literal">meta</tt>: 是否按下了 meta 键</li>
    <li><tt class="docutils literal">shift</tt>: 是否按下了 shift 键</li>
    </ul>
    </dd>
    </dl>

<dl class="docutils">
<dt>为方便起见，<tt class="docutils literal">KeyboardKey</tt> 对象还具有以下属性：</dt>
<dd><ul class="first last simple">
<li><tt class="docutils literal">is_cursorkey</tt>: 键是否为光标（箭头）键</li>
<li><tt class="docutils literal">number</tt>: 数字键的整数值 (0-9，其他键为 <tt class="docutils literal">None</tt>)</li>
<li><tt class="docutils literal">backspace</tt>, <tt class="docutils literal">tab</tt>, <tt class="docutils literal">enter</tt>, <tt class="docutils literal">shift</tt>, <tt class="docutils literal">control</tt>, <tt class="docutils literal">alt</tt>, <tt class="docutils literal">pause</tt>, <tt class="docutils literal">caps_lock</tt>, <tt class="docutils literal">escape</tt>, <tt class="docutils literal">space</tt>,
<tt class="docutils literal">page_up</tt>, <tt class="docutils literal">page_down</tt>, <tt class="docutils literal">end</tt>, <tt class="docutils literal">home</tt>, <tt class="docutils literal">arrow_left</tt>, <tt class="docutils literal">arrow_up</tt>, <tt class="docutils literal">arrow_right</tt>, <tt class="docutils literal">arrow_down</tt>,
<tt class="docutils literal">print_screen</tt>, <tt class="docutils literal">insert</tt>, <tt class="docutils literal">delete</tt>, <tt class="docutils literal">meta</tt>,
<tt class="docutils literal">f1</tt>, <tt class="docutils literal">f2</tt>, <tt class="docutils literal">f3</tt>, <tt class="docutils literal">f4</tt>, <tt class="docutils literal">f5</tt>, <tt class="docutils literal">f6</tt>, <tt class="docutils literal">f7</tt>, <tt class="docutils literal">f8</tt>, <tt class="docutils literal">f9</tt>, <tt class="docutils literal">f10</tt>, <tt class="docutils literal">f11</tt>, <tt class="docutils literal">f12</tt>: 该键是否为相应的键</li>
</ul>
</dd>
</dl>

|  参数 | 描述 |
| --- | --- |
| on_key: | 键盘事件发生时执行的回调。 |
| active: | 一个布尔标志，指示是否应执行回调（默认值：<tt class="docutils literal">True</tt>） |
| repeating: | 一个布尔标志，指示是否应重复发送按住的键（默认值：<tt class="docutils literal">True</tt>） |
| ignore: | 当这些元素类型之一获得焦点时忽略键（默认值：<tt class="docutils literal">['input', 'select', 'button', 'textarea']</tt>） |

```python
from nicegui import ui
from nicegui.events import KeyEventArguments

def handle_key(e: KeyEventArguments):
    if e.key == 'f' and not e.action.repeat:
        if e.action.keyup:
            ui.notify('f was just released')
        elif e.action.keydown:
            ui.notify('f was just pressed')
    if e.modifiers.shift and e.action.keydown:
        if e.key.arrow_left:
            ui.notify('going left')
        elif e.key.arrow_right:
            ui.notify('going right')
        elif e.key.arrow_up:
            ui.notify('going up')
        elif e.key.arrow_down:
            ui.notify('going down')

keyboard = ui.keyboard(on_key=handle_key)
ui.label('Key events can be caught globally by using the keyboard element.')
ui.checkbox('Track key events').bind_value_to(keyboard, 'active')

ui.run()
```

## Reference

### Initializer

|  参数 | 描述 |
| --- | --- |
| on_key: | 键盘事件发生时执行的回调。 |
| active: | 一个布尔标志，指示是否应执行回调（默认值：<tt class="docutils literal">True</tt>） |
| repeating: | 一个布尔标志，指示是否应重复发送按住的键（默认值：<tt class="docutils literal">True</tt>） |
| ignore: | 当这些元素类型之一获得焦点时忽略键（默认值：<tt class="docutils literal">['input', 'select', 'button', 'textarea']</tt>） |

### Properties

**`active`**`: BindableProperty`

**`classes`**`: 'Classes[Self]'`

元素的类。

**`is_deleted`**`: 'bool'`

元素是否已被删除。

**`is_ignoring_events`**`: 'bool'`

返回元素当前是否忽略事件。

**`props`**`: 'Props[Self]'`

元素的 props。

**`style`**`: 'Style[Self]'`

元素的样式。

**`visible`**`: BindableProperty`

### Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

将资源添加到元素。

|  参数 | 描述 |
| --- | --- |
| path: | 资源路径 (例如：包含 CSS 和 JavaScript 文件的文件夹) |

**`add_slot`**`(name: str, template: Optional[str] = None) -> Slot`

向元素添加一个slot.

NiceGUI 使用 Vue 中的 slot 概念：
元素可以有多个 slot，每个 slot 可能都有多个子元素。
大多数元素只有一个 slot，例如 `ui.card` (QCard) 只有一个默认 slot。
但是，更复杂的元素（如 `ui.table` (QTable)）可以有更多 slot，例如 “header”、“body” 等。
如果使用 `ui.row(): ...` 通过 with 嵌套 NiceGUI 元素，则将新元素放置在行的默认 slot 中。
但是，如果使用 with `table.add_slot(...): ...`，则会进入不同的 slot。

slot 堆栈可帮助 NiceGUI 跟踪当前哪个 slot 用于新元素。
`parent` 字段保存对其元素的引用。
每当通过 with 表达式进入元素时，其默认 slot 也会自动进入。

|  参数 | 描述 |
| --- | --- |
| name: | slot 的名称 |
| template: | slot 的 Vue 模板 |
| return: | slot |

**`ancestors`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的祖先。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定是双向的，从该元素到目标，以及从目标到该元素。
更新会立即发生，并在值更改时发生。
对于初始同步，向后绑定优先。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

从此元素的可见性从目标对象的 target_name 属性绑定。

绑定仅以一种方式工作，从目标到此元素。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| backward: | 在将值应用于此元素之前应用于该值的函数。 |
| value: | 如果指定，则仅当目标值等于此值时，该元素才可见。 |

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

将此元素的可见性绑定到目标对象的 target_name 属性。

绑定仅以一种方式工作，从该元素到目标。
更新会立即发生，并在值更改时发生。

|  参数 | 描述 |
| --- | --- |
| target_object: | 要绑定的对象。 |
| target_name: | 要绑定的属性的名称。 |
| forward: | 在将值应用于目标之前应用于该值的函数。 |

**`clear`**`() -> None`

删除所有子元素。

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除、切换或替换默认 HTML 类。

这允许使用 <a class="reference external" href="https://tailwindcss.com/">Tailwind</a> 或 <a class="reference external" href="https://quasar.dev/">Quasar</a> 类来修改元素的外观或其布局。

如果不需要预定义的类，则删除或替换类可能会有所帮助。
此类的所有元素将共享这些 HTML 类。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 空格分隔的类字符串 |
| remove: | 从元素中删除的空格分隔的类字符串 |
| toggle: | 要切换的空格分隔的类字符串 |
| replace: | 用于替换现有类的空格分隔的类字符串 |

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

添加或删除默认 props。

这允许使用 <a class="reference external" href="https://quasar.dev/">Quasar</a> props 修改元素的外观或其布局。
由于 props 只是作为 HTML 属性应用，因此它们可以与任何 HTML 元素一起使用。
此类的所有元素将共享这些 props。
这些必须在元素实例化之前定义。

如果未指定值，则假定布尔属性为 <tt class="docutils literal">True</tt>。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加的布尔值或键值对的空格分隔列表 |
| remove: | 要删除的属性键的空格分隔列表 |

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

应用、删除或替换默认 CSS 定义。

如果不需要预定义的样式，则删除或替换样式可能会有所帮助。
此类的所有元素将共享这些 CSS 定义。
这些必须在元素实例化之前定义。

|  参数 | 描述 |
| --- | --- |
| add: | 要添加到元素的分号分隔的样式列表 |
| remove: | 从元素中删除的分号分隔的样式列表 |
| replace: | 要使用的分号分隔的样式列表，以替换现有的样式 |

**`delete`**`() -> None`

删除该元素及其所有子元素。

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

迭代元素的后代。

|  参数 | 描述 |
| --- | --- |
| include_self: | 是否在迭代中包含元素本身 |

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

返回一个计算属性。

应等待此函数，以便正确返回计算的属性。

|  参数 | 描述 |
| --- | --- |
| prop_name: | 计算属性的名称 |
| timeout: | 等待响应的最长时间（默认为 1 秒） |

**`mark`**`(*markers: str) -> Self`

替换元素的标记。

标记用于使用 <a class="reference external" href="/documentation/element_filter">ElementFilter</a> 查询来标识元素，这在测试中大量使用，
但也可以用于减少全局变量的数量或传递依赖项。

|  参数 | 描述 |
| --- | --- |
| markers: | 字符串列表或带有空格分隔标记的单个字符串；替换现有标记 |

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

将元素移动到另一个容器。

|  参数 | 描述 |
| --- | --- |
| target_container: | 要将元素移动到的容器（默认值：父容器） |
| target_index: | 目标槽内的索引（默认值：追加到末尾） |
| target_slot: | 目标容器内的槽（默认值：默认槽） |

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

订阅一个事件。

|  参数 | 描述 |
| --- | --- |
| type: | 事件的名称（例如“click”、“mousedown”或“update:model-value”） |
| handler: | 事件发生时调用的回调 |
| args: |  包含在发送到事件处理程序的消息中的参数（默认值：<cite>None</cite> 表示全部） |
| throttle: | 事件发生之间的最短时间（以秒为单位）（默认值：0.0） |
| leading_events: |  是否在第一次事件发生时立即触发事件处理程序（默认值：<cite>True</cite>） |
| trailing_events: |  是否在最后一次事件发生后触发事件处理程序（默认值：<cite>True</cite>） |
| js_handler: |  事件发生时执行的 JavaScript 代码，例如 <cite>(evt) =&gt; alert(evt)</cite> （默认值：<cite>None</cite>） |

**`on_key`**`(handler: Union[Callable[[nicegui.events.KeyEventArguments], Any], Callable[[], Any]]) -> Self`

添加在键盘事件发生时调用的回调。

**`remove`**`(element: Union[Element, int]) -> None`

删除子元素。

|  参数 | 描述 |
| --- | --- |
| element: | 元素实例或其 ID |

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

在客户端运行一个方法。

如果等待该函数，则返回方法调用的结果。
否则，该方法将执行而不等待响应。

|  参数 | 描述 |
| --- | --- |
| name: | 方法的名称 |
| args: | 传递给方法的参数 |
| timeout: | 等待响应的最长时间（默认为 1 秒） |

**`set_visibility`**`(visible: bool) -> None`

设置此元素的可见性。

|  参数 | 描述 |
| --- | --- |
| visible: | 元素是否应该可见。 |

**`tooltip`**`(text: str) -> Self`

向元素添加工具提示。

|  参数 | 描述 |
| --- | --- |
| text: | 工具提示的文本 |

**`update`**`() -> None`

在客户端更新元素。

### Inheritance

*   `Element`
*   `Visibility`
