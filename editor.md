# ui.editor

## Editor

一个基于 [Quasar 的 QEditor](https://quasar.dev/vue-components/editor) 的 WYSIWYG 编辑器。
该值是一个字符串，其中包含格式化为 HTML 代码的文本。

| 参数      | 描述                               |
| --------- | ---------------------------------- |
| value:    | 初始值                               |
| on_change: | 当值改变时要调用的回调函数                |

```python
from nicegui import ui

editor = ui.editor(placeholder='Type something here')
ui.markdown().bind_content_from(editor, 'value',
                                backward=lambda v: f'HTML code:\n```\n{v}\n```')

ui.run()
```

## Reference

## Initializer

| 参数      | 描述                               |
| --------- | ---------------------------------- |
| value:    | 初始值                               |
| on_change: | 当值改变时要调用的回调函数                |

## Properties

**`classes`**`: 'Classes[Self]'`

<div class="codehilite">
<p>The classes of the element.</p>
</div>

**`enabled`**`: BindableProperty`

**`is_deleted`**`: 'bool'`

<div class="codehilite">
<p>Whether the element has been deleted.</p>
</div>

**`is_ignoring_events`**`: bool`

<div class="codehilite">
<p>Return whether the element is currently ignoring events.</p>
</div>

**`props`**`: 'Props[Self]'`

<div class="codehilite">
<p>The props of the element.</p>
</div>

**`style`**`: 'Style[Self]'`

<div class="codehilite">
<p>The style of the element.</p>
</div>

**`value`**`: BindableProperty`

**`visible`**`: BindableProperty`

## Methods

**`add_resource`**`(path: Union[str, Path]) -> None`

<div class="codehilite">
<p>Add a resource to the element.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param path:</th>
<td class="field-body">path to the resource (e.g. folder with CSS and JavaScript files)</td>
</tr>
</tbody>
</table>
</div>

**`add_slot`**`(name: str, template: Optional[str] = None) -> Slot`

<div class="codehilite">
<p>Add a slot to the element.</p>
<p>NiceGUI is using the slot concept from Vue:
Elements can have multiple slots, each possibly with a number of children.
Most elements only have one slot, e.g. a <cite>ui.card</cite> (QCard) only has a default slot.
But more complex elements like <cite>ui.table</cite> (QTable) can have more slots like "header", "body" and so on.
If you nest NiceGUI elements via with <cite>ui.row(): ...</cite> you place new elements inside of the row's default slot.
But if you use with <cite>table.add_slot(...): ...</cite>, you enter a different slot.</p>
<p>The slot stack helps NiceGUI to keep track of which slot is currently used for new elements.
The <cite>parent</cite> field holds a reference to its element.
Whenever an element is entered via a <cite>with</cite> expression, its default slot is automatically entered as well.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param name:</th>
<td class="field-body">name of the slot</td>
</tr>
<tr class="field">
<th class="field-name">param template:</th>
<td class="field-body">Vue template of the slot</td>
</tr>
<tr class="field">
<th class="field-name">return:</th>
<td class="field-body">the slot</td>
</tr>
</tbody>
</table>
</div>

**`ancestors`**`(include_self: bool = False) -> Iterator[Element]`

<div class="codehilite">
<p>Iterate over the ancestors of the element.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param include_self:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">whether to include the element itself in the iteration</td>
</tr>
</tbody>
</table>
</div>

**`bind_enabled`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

<div class="codehilite">
<p>Bind the enabled state of this element to the target object's target_name property.</p>
<p>The binding works both ways, from this element to the target and from the target to this element.
The update happens immediately and whenever a value changes.
The backward binding takes precedence for the initial synchronization.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_object:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The object to bind to.</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The name of the property to bind to.</td>
</tr>
<tr class="field">
<th class="field-name">param forward:</th>
<td class="field-body">A function to apply to the value before applying it to the target.</td>
</tr>
<tr class="field">
<th class="field-name">param backward:</th>
<td class="field-body">A function to apply to the value before applying it to this element.</td>
</tr>
</tbody>
</table>
</div>

**`bind_enabled_from`**`(target_object: Any, target_name: str = 'enabled', backward: Callable[..., Any] = [...]) -> Self`

<div class="codehilite">
<p>Bind the enabled state of this element from the target object's target_name property.</p>
<p>The binding works one way only, from the target to this element.
The update happens immediately and whenever a value changes.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_object:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The object to bind from.</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The name of the property to bind from.</td>
</tr>
<tr class="field">
<th class="field-name">param backward:</th>
<td class="field-body">A function to apply to the value before applying it to this element.</td>
</tr>
</tbody>
</table>
</div>

**`bind_enabled_to`**`(target_object: Any, target_name: str = 'enabled', forward: Callable[..., Any] = [...]) -> Self`

<div class="codehilite">
<p>Bind the enabled state of this element to the target object's target_name property.</p>
<p>The binding works one way only, from this element to the target.
The update happens immediately and whenever a value changes.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_object:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The object to bind to.</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The name of the property to bind to.</td>
</tr>
<tr class="field">
<th class="field-name">param forward:</th>
<td class="field-body">A function to apply to the value before applying it to the target.</td>
</tr>
</tbody>
</table>
</div>

**`bind_value`**`(target_object: Any, target_name: str = 'value', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...]) -> Self`

<div class="codehilite">
<p>Bind the value of this element to the target object's target_name property.</p>
<p>The binding works both ways, from this element to the target and from the target to this element.
The update happens immediately and whenever a value changes.
The backward binding takes precedence for the initial synchronization.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_object:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The object to bind to.</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The name of the property to bind to.</td>
</tr>
<tr class="field">
<th class="field-name">param forward:</th>
<td class="field-body">A function to apply to the value before applying it to the target.</td>
</tr>
<tr class="field">
<th class="field-name">param backward:</th>
<td class="field-body">A function to apply to the value before applying it to this element.</td>
</tr>
</tbody>
</table>
</div>

**`bind_value_from`**`(target_object: Any, target_name: str = 'value', backward: Callable[..., Any] = [...]) -> Self`

<div class="codehilite">
<p>Bind the value of this element from the target object's target_name property.</p>
<p>The binding works one way only, from the target to this element.
The update happens immediately and whenever a value changes.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_object:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The object to bind from.</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The name of the property to bind from.</td>
</tr>
<tr class="field">
<th class="field-name">param backward:</th>
<td class="field-body">A function to apply to the value before applying it to this element.</td>
</tr>
</tbody>
</table>
</div>

**`bind_value_to`**`(target_object: Any, target_name: str = 'value', forward: Callable[..., Any] = [...]) -> Self`

<div class="codehilite">
<p>Bind the value of this element to the target object's target_name property.</p>
<p>The binding works one way only, from this element to the target.
The update happens immediately and whenever a value changes.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_object:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The object to bind to.</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The name of the property to bind to.</td>
</tr>
<tr class="field">
<th class="field-name">param forward:</th>
<td class="field-body">A function to apply to the value before applying it to the target.</td>
</tr>
</tbody>
</table>
</div>

**`bind_visibility`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...], backward: Callable[..., Any] = [...], value: Any = None) -> Self`

<div class="codehilite">
<p>Bind the visibility of this element to the target object's target_name property.</p>
<p>The binding works both ways, from this element to the target and from the target to this element.
The update happens immediately and whenever a value changes.
The backward binding takes precedence for the initial synchronization.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_object:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The object to bind to.</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The name of the property to bind to.</td>
</tr>
<tr class="field">
<th class="field-name">param forward:</th>
<td class="field-body">A function to apply to the value before applying it to the target.</td>
</tr>
<tr class="field">
<th class="field-name">param backward:</th>
<td class="field-body">A function to apply to the value before applying it to this element.</td>
</tr>
<tr class="field">
<th class="field-name">param value:</th>
<td class="field-body">If specified, the element will be visible only when the target value is equal to this value.</td>
</tr>
</tbody>
</table>
</div>

**`bind_visibility_from`**`(target_object: Any, target_name: str = 'visible', backward: Callable[..., Any] = [...], value: Any = None) -> Self`

<div class="codehilite">
<p>Bind the visibility of this element from the target object's target_name property.</p>
<p>The binding works one way only, from the target to this element.
The update happens immediately and whenever a value changes.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_object:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The object to bind from.</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The name of the property to bind from.</td>
</tr>
<tr class="field">
<th class="field-name">param backward:</th>
<td class="field-body">A function to apply to the value before applying it to this element.</td>
</tr>
<tr class="field">
<th class="field-name">param value:</th>
<td class="field-body">If specified, the element will be visible only when the target value is equal to this value.</td>
</tr>
</tbody>
</table>
</div>

**`bind_visibility_to`**`(target_object: Any, target_name: str = 'visible', forward: Callable[..., Any] = [...]) -> Self`

<div class="codehilite">
<p>Bind the visibility of this element to the target object's target_name property.</p>
<p>The binding works one way only, from this element to the target.
The update happens immediately and whenever a value changes.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_object:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The object to bind to.</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">The name of the property to bind to.</td>
</tr>
<tr class="field">
<th class="field-name">param forward:</th>
<td class="field-body">A function to apply to the value before applying it to the target.</td>
</tr>
</tbody>
</table>
</div>

**`clear`**`() -> None`

<div class="codehilite">
<p>Remove all child elements.</p>
</div>

**`default_classes`**`(add: Optional[str] = None, remove: Optional[str] = None, toggle: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

<div class="codehilite">
<p>Apply, remove, toggle, or replace default HTML classes.</p>
<p>This allows modifying the look of the element or its layout using <a class="reference external" href="https://tailwindcss.com/">Tailwind</a> or <a class="reference external" href="https://quasar.dev/">Quasar</a> classes.</p>
<p>Removing or replacing classes can be helpful if predefined classes are not desired.
All elements of this class will share these HTML classes.
These must be defined before element instantiation.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param add:</th>
<td class="field-body">whitespace-delimited string of classes</td>
</tr>
<tr class="field">
<th class="field-name">param remove:</th>
<td class="field-body">whitespace-delimited string of classes to remove from the element</td>
</tr>
<tr class="field">
<th class="field-name">param toggle:</th>
<td class="field-body">whitespace-delimited string of classes to toggle</td>
</tr>
<tr class="field">
<th class="field-name">param replace:</th>
<td class="field-body">whitespace-delimited string of classes to use instead of existing ones</td>
</tr>
</tbody>
</table>
</div>

**`default_props`**`(add: Optional[str] = None, remove: Optional[str] = None) -> type[Self]`

<div class="codehilite">
<p>Add or remove default props.</p>
<p>This allows modifying the look of the element or its layout using <a class="reference external" href="https://quasar.dev/">Quasar</a> props.
Since props are simply applied as HTML attributes, they can be used with any HTML element.
All elements of this class will share these props.
These must be defined before element instantiation.</p>
<p>Boolean properties are assumed <tt class="docutils literal">True</tt> if no value is specified.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param add:</th>
<td class="field-body">whitespace-delimited list of either boolean values or key=value pair to add</td>
</tr>
<tr class="field">
<th class="field-name">param remove:</th>
<td class="field-body">whitespace-delimited list of property keys to remove</td>
</tr>
</tbody>
</table>
</div>

**`default_style`**`(add: Optional[str] = None, remove: Optional[str] = None, replace: Optional[str] = None) -> type[Self]`

<div class="codehilite">
<p>Apply, remove, or replace default CSS definitions.</p>
<p>Removing or replacing styles can be helpful if the predefined style is not desired.
All elements of this class will share these CSS definitions.
These must be defined before element instantiation.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param add:</th>
<td class="field-body">semicolon-separated list of styles to add to the element</td>
</tr>
<tr class="field">
<th class="field-name">param remove:</th>
<td class="field-body">semicolon-separated list of styles to remove from the element</td>
</tr>
<tr class="field">
<th class="field-name">param replace:</th>
<td class="field-body">semicolon-separated list of styles to use instead of existing ones</td>
</tr>
</tbody>
</table>
</div>

**`delete`**`() -> None`

<div class="codehilite">
<p>Delete the element and all its children.</p>
</div>

**`descendants`**`(include_self: bool = False) -> Iterator[Element]`

<div class="codehilite">
<p>Iterate over the descendants of the element.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param include_self:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">whether to include the element itself in the iteration</td>
</tr>
</tbody>
</table>
</div>

**`disable`**`() -> None`

<div class="codehilite">
<p>Disable the element.</p>
</div>

**`enable`**`() -> None`

<div class="codehilite">
<p>Enable the element.</p>
</div>

**`get_computed_prop`**`(prop_name: str, timeout: float = 1) -> AwaitableResponse`

<div class="codehilite">
<p>Return a computed property.</p>
<p>This function should be awaited so that the computed property is properly returned.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param prop_name:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">name of the computed prop</td>
</tr>
<tr class="field">
<th class="field-name">param timeout:</th>
<td class="field-body">maximum time to wait for a response (default: 1 second)</td>
</tr>
</tbody>
</table>
</div>

**`mark`**`(*markers: str) -> Self`

<div class="codehilite">
<p>Replace markers of the element.</p>
<p>Markers are used to identify elements for querying with <a class="reference external" href="/documentation/element_filter">ElementFilter</a>
which is heavily used in testing
but can also be used to reduce the number of global variables or passing around dependencies.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param markers:</th>
<td class="field-body">list of strings or single string with whitespace-delimited markers; replaces existing markers</td>
</tr>
</tbody>
</table>
</div>

**`move`**`(target_container: Optional[Element] = None, target_index: int = -1, target_slot: Optional[str] = None) -> None`

<div class="codehilite">
<p>Move the element to another container.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name" colspan="2">param target_container:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">container to move the element to (default: the parent container)</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_index:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">index within the target slot (default: append to the end)</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param target_slot:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">slot within the target container (default: default slot)</td>
</tr>
</tbody>
</table>
</div>

**`on`**`(type: str, handler: Optional[events.Handler[events.GenericEventArguments]] = None, args: Union[None, Sequence[str], Sequence[Optional[Sequence[str]]]] = None, throttle: float = 0.0, leading_events: bool = True, trailing_events: bool = True, js_handler: Optional[str] = None) -> Self`

<div class="codehilite">
<p>Subscribe to an event.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param type:</th>
<td class="field-body">name of the event (e.g. "click", "mousedown", or "update:model-value")</td>
</tr>
<tr class="field">
<th class="field-name">param handler:</th>
<td class="field-body">callback that is called upon occurrence of the event</td>
</tr>
<tr class="field">
<th class="field-name">param args:</th>
<td class="field-body">arguments included in the event message sent to the event handler (default: <cite>None</cite> meaning all)</td>
</tr>
<tr class="field">
<th class="field-name">param throttle:</th>
<td class="field-body">minimum time (in seconds) between event occurrences (default: 0.0)</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param leading_events:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">whether to trigger the event handler immediately upon the first event occurrence (default: <cite>True</cite>)</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param trailing_events:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">whether to trigger the event handler after the last event occurrence (default: <cite>True</cite>)</td>
</tr>
<tr class="field">
<th class="field-name" colspan="2">param js_handler:</th>
</tr>
<tr class="field">
<td>&nbsp;</td>
<td class="field-body">JavaScript code that is executed upon occurrence of the event, e.g. <cite>(evt) =&gt; alert(evt)</cite> (default: <cite>None</cite>)</td>
</tr>
</tbody>
</table>
</div>

**`on_value_change`**`(callback: Union[Callable[[nicegui.events.ValueChangeEventArguments], Any], Callable[[], Any]]) -> Self`

<div class="codehilite">
<p>Add a callback to be invoked when the value changes.</p>
</div>

**`remove`**`(element: Union[Element, int]) -> None`

<div class="codehilite">
<p>Remove a child element.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param element:</th>
<td class="field-body">either the element instance or its ID</td>
</tr>
</tbody>
</table>
</div>

**`run_method`**`(name: str, *args: Any, timeout: float = 1) -> AwaitableResponse`

<div class="codehilite">
<p>Run a method on the client side.</p>
<p>If the function is awaited, the result of the method call is returned.
Otherwise, the method is executed without waiting for a response.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param name:</th>
<td class="field-body">name of the method</td>
</tr>
<tr class="field">
<th class="field-name">param args:</th>
<td class="field-body">arguments to pass to the method</td>
</tr>
<tr class="field">
<th class="field-name">param timeout:</th>
<td class="field-body">maximum time to wait for a response (default: 1 second)</td>
</tr>
</tbody>
</table>
</div>

**`set_enabled`**`(value: bool) -> None`

<div class="codehilite">
<p>Set the enabled state of the element.</p>
</div>

**`set_value`**`(value: Any) -> None`

<div class="codehilite">
<p>Set the value of this element.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param value:</th>
<td class="field-body">The value to set.</td>
</tr>
</tbody>
</table>
</div>

**`set_visibility`**`(visible: bool) -> None`

<div class="codehilite">
<p>Set the visibility of this element.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param visible:</th>
<td class="field-body">Whether the element should be visible.</td>
</tr>
</tbody>
</table>
</div>

**`tooltip`**`(text: str) -> Self`

<div class="codehilite">
<p>Add a tooltip to the element.</p>
<table>
<colgroup>
<col class="field-name">
<col class="field-body">
</colgroup>
<tbody valign="top">
<tr class="field">
<th class="field-name">param text:</th>
<td class="field-body">text of the tooltip</td>
</tr>
</tbody>
</table>
</div>

**`update`**`() -> None`

<div class="codehilite">
<p>Update the element on the client side.</p>
</div>

## Inheritance

*   `ValueElement`
*   `DisableableElement`
*   `Element`
*   `Visibility`
