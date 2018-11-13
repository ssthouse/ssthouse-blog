# Arrtibute VS Property



### First question:

**what is attribute && what is property ?**

**attribute** are the key:value pare you write in HTML code, eg:

```html
<input id="the-input" type="text" value="Name:">
```

there are three attribute in above code:

- id : the-input
- type : text
- value : Name:

property is what attributy is attached to DOM Object’s field (property), eg:

```javascript
HTMLInputElement.id === 'the-input'
HTMLInputElement.type === 'text'
HTMLInputElement.value === 'Name:'
```

### Second question:

From above example. it seems like the attribute is same as property. so what’s the difference ?

let’s say we have another piece of html code:

```html
<input id="the-input" type="typo" value="Name:">
// after onload, user input his name in this component~ 'Jack'
```

After that. let’s check the values of attribute & property:

```javascript
// attribute still remains the original value

input.getAttribute('id') === 'the-input'
input.getAttribute('type') === 'typo'
input.getAttribute('value') === 'Name:'

// property is a different story
input.id // the-input
input.type //  text
input.value // Jack
```

actually, we can see from the name of these two words: 

**arrtibute** sounds more like it’s **uneditable**

while **property** sounds more like it will be changed in the lifecycle.



### Additional knowledge

notice that, there are some special attributes which’s name is different from property name. for now, here is a list what I know:

- for => htmlFor
- class => className

(actually we can find out the list if we dig into the DOM source code, but it doesn’t seems worthy…)



Besides, here are some discussion about if it’s good to let have both property and attribute:

https://stackoverflow.com/a/6377829/5033286

in that, some one said the attribute **value** is correspond to attribute **defaultValue**. while value in DOM Node object is a totally different thing.

also, **check**  => **defaultChecked** 



for the attribute’s value spec, here is a list we can check:

https://www.w3.org/TR/html5/infrastructure.html#reflect