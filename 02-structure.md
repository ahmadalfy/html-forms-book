[TOC]

# Forms structure

HTML offers a variety of elements that can be used collect data from users. Due to the increase in the complexity of the web applications, developers are still creating custom components that extend existing elements and add new ones. In this chapter we will explore the native HTML elements used in HTML forms.

## Forms 101

### The `form` tag

Forms always start with the `form` tag. It's used to define the *method* that will be used to submit the form data to the server and the *URL* where it will be submitted.

```html
<form method="POST" action="/mailer.php">
  <!-- the form data shall be written here -->
</form>
```

The example above shows 2 different attributes written in the `form` tag. The `method` and the `action` attributes. In the upcoming sections for the sake of simplicity, these attributes will be removed.

#### The `method` attribute

The `method` attribute describes the HTTP verb used to submit the form. It's case insensitive and can be either:

1. `get` which is the default value. The form data is submitted in the URL as *query string*. This method is usually used when we want to create an indexable URL (like a search form).
2. `post` method, the data is sent in the request body.

💡 If the author does not provide a `method` attribute, the form will use the `get` method by default.

💡 HTML5 introduced a new method `dialog` which can be used to close a native `dialog` element that enclose the form. For the sake of simplicity, this one will be discussed later in **Real life cases** section.

⚠️ `form` tag cannot be nested (i.e we cannot add a form inside another form). Browsers usually ignore the inner `form` tag. Some authors try to do that in attempt to organize their form into different sections. This is done using different elements that we will discuss later.

#### The `action` attribute

The `action` attribute is used to define the URL where this form will be submitted. If this attribute is not available, the form will submit to the same URL. This URL can be overridden by some form elements that we will explore later.

### Form elements used to collect data

In the past section we created the form basic structure. In this section we will learn about the different elements used to collect inputs from the users. We will discuss the `input`, `textarea` and `select` tags. For the same of simplicity, we will refer to them collectively as *form controls*.

#### The `input` tag

The `input` tag is used to collect data from the user. It has a lot of types that control the type of data it accepts. These types define the user interaction with that element and the kind of validation rules that will be triggered when it receives data.

```html
<form>
  <label for="first-name">First Name</label>
  <input type="text" id="first-name" />
  ...
</form>
```

You can see there is a new tag we used here `label`. For now, let's just say that it is used to describe a form input. We will talk more about it in the next section.

The `input` is a self-closing element. The `type` attribute defines what type of the element is. HTML5 introduced a lot of different types that can be used to collect different types of data.

##### Old input types

* `text` accepts anything. This is the default value of the attribute. If the user doesn't provide a value for the `type`, the browser will consider the `input` type as `text`. Also, if the browser doesn't understand the value of the `type` attribute (like if it is an invalid value, or an older browser that doesn't understand new values) it will also be treated as `text`.
* `password` accepts anything like text. The difference is, the browser will attempt to hide the value the user is writing on the screen for privacy purpose. The behavior is different on mobile phones where the browser will attempt to show the last character you wrote for a moment before hiding the entire value.
* `checkbox` allows the user to choose multiple answers for one question. This one is suitable to use when the available options are few and your intent is to show them all together.
* `radio` requires the user to pick one answer out of many.
* `file` allows the user to pick one or more files from their machine.
* `hidden` allows developers to include data when the form is not submitted without being visible to the users. It can be used in different scenarios, most commonly to include an automatically generated token to prevent an attack like Cross Site Request Forgery (CSRF). We will discuss this part in the forms security section.
* `submit` transforms the `input` to look like a button that can be used to submit form. Since the introduction of the `button` element, authors shifted to the new element to submit forms because it offers richer render possibilities.
* `reset` transforms the `input` to look like a button that can be used to reset the value of the form to match the corresponding `value` attribute on each element. Like the `submit` type, it can be replaced with a `button` tag with the same `type` which is now preferred for the same reason.

##### HTML5 new input types

* `email` accepts email format. This will trigger validation errors and prevent form submission if the value provided doesn't match the email format.
* `number` accepts numerical values. According to the specs it accepts floating-point numbers so exponential notation is accepted as well. We will discuss this later.
* `date` accepts date format. This input display a mini calendar that allows the user to pick a date. The layout of that date picker differs from a browser to another and is affected by the device type and operating system as well.
* `color` accepts color in a hexadecimal format. This input display a color picker for the user to choose. The layout of that color picker is dependent upon the operating system.
* `datetime-local`
* `range`
* `search`
* `tel`
* `url`

##### How `input`'s value is calculated

The `input` tag has the option to accept an initial value using the `value` attribute. If that attribute is missing, the value will be nothing. When the user submit a form, the text value written inside the form will be submitted.

Some input types will resolve to special values that is different from the text being displayed inside the form control. For example, an `input` with the type `date` might display the date in the format `MM/DD/YYYY` (if your locale is en-US) but the submitted value will always be formatted according to ISO8601 like `YYYY-MM-DD`. These special cases will be explained when we discuss each `type` separately.

#### The `textarea` tag

The `textarea` allows the user to write multiple lines of text. It's suitable to use when the user is expected to insert big chunks of unstructured text (like a message, a feedback ... etc).

```html
<form>
  <label for="address">Address</label>
  <textarea id="address"></textarea>
</form>
```

Unlike the other form elements, the `textarea` does not require a value attribute. If we want to write a default value for a `textarea` we simply write it as text inside the tag itself (note that this isn't a self-closing tag like `input`)

```html
<form>
  <label for="address">Address</label>
  <textarea id="address">1600 Amphitheatre Parkway in Mountain View,
    California,
    United States</textarea>
</form>
```

⚠️ If you used a framework [like react](https://reactjs.org/docs/forms.html#the-textarea-tag), you will find that the binding of `textarea` value happens by using `value` attribute. The framework changes that on run time to work as we described above. The `value` attribute doesn't work on `textarea`.

#### The `select` tag

The `select` tag let the user select an option or more from a list. The available options we provide for the user to pick from is listed using the `option` tag. The `select` tag provides a similar functionality to that of the `input` of type `checkbox` or `radio`.

A good use case for `select` is when there are too many options to display on the screen. The available options are only demonstrated when the user *open* the select control. check the following example:

```html
<form>
  <label for="car-brand">Your car brand</label>
  <select id="car-brand">
    <option>Acura</option>
    <option>Alfa Romeo</option>
    <option>Audi</option>
    <option>BMW</option>
    <option>Bentley</option>
		...
  </select>
</form>
```

There are tens of car brands. Displaying all of them using checkboxes or radio buttons will unnecessarily occupy a huge space.

Another good use case is when  is when we want to group the different options together in a way that makes it easy for the user to understand. Check the following:

```html
<form>
  <label for="car-model">Your car model</label>
  <select id="car-model">
    <optgroup label="Honda Civic">
    	<option>2020 LX	Sedan</option>
    	<option>2020 LX	Hatchback</option>
    	<option>2020 EX	Sedan</option>
    	<option>2020 Touring Sedan</option>
    </optgroup>
    <optgroup label="Ford">
      <option>2020 Edge ST</option>
      <option>F-150 Raptor</option>
      <option>2020 Mustang Shelby GT350</option>
    </optgroup>
  </select>
</form>
```

The `optgroup` tag accepts a `label` attribute. Its value will be displayed on top of these options making it easier to distinguish for the user. It will also add the needed padding for each group of options to make it easier to read.

A great benefit we get from `optgroup` is that we can disable a group of options by using the `disabled` attribute on that group.

⚠️ With the power offered by the `optgroup`, some authors nest `optgroup`  inside each other. While this might work in some browser, it goes again the HTML specification and hence, it produces invalid HTML which could lead to unexpected results. The HTML specs define that the only allowed children of `optgroup` is zero or more `option` tags. Also, the only allowed parent is the `select` tag. Try to stick to that and keep your forms simple.

##### How `select`'s value is calculated

The value of the `select` tag is determined by the selected `option`. This leads to a very interesting behavior.

* If the selected `option` does not have a `value` attribute, the value will be the text written inside the `option` tag.
* If the selection `option` have a `value` attribute, this value of this attribute will be used.

### Structural form elements

HTML provides us with a few elements that can be used to improve structuring and laying out our forms. In this section we will be talking about `label`, `fields` and `legend`.

#### The `label` tag

You were introduced to the `label` tag in the previous section. As we described earlier; it is used to provide a caption for form controls. To associate a `label` with a form controls we have two options:

```html
<form>
  <!--First option -->
  <label for="unique-identifier">Label text</label>
  <input id="unique-identifier" type="text" />
  
  <!--Second option -->
  <label>
    Label text
    <input type="text" />
  </label>
</form>
```

The first option requires that we specify the `id` of that control to the value of the `for` attribute of the `label`. The second option involves wrapping that form control inside the `label` along with the text used to describe it. Both options are fine but the first one offers a greater styling control over the text. Also, no styles will be cascaded from the `label` to the enclosed form control.

It's always encouraged to use `label`s for the following benefits:

1. It helps the users who use assistive technologies like screen reader. It will read out the label when the user is focused on the form input, helping the user to understand what this field is about and what data should be entered.
2. When the user clicks a `label`, it will activate the associated `input`. This offers great benefit when a user tries to activate a checkbox or a radio button. Instead of having to click precisely on the `input`, clicking the whole text will do the purpose. This is mostly noted when a user try to fill a form on a mobile device.

💡 We should always use labels to describe form controls. Sometimes, designers don't add labels on simple forms (like a search form with a single input) or replace them with `placeholders` (we will discuss it later). In the accessibility section, we will discuss some alternative approaches to provide form controls with descriptive text; either through `label` or other techniques.

💡 Multiple labels can be associated with a single form control. This becomes handy when you try to design a certain experience where multiple click areas can be used to activate the same form control.

#### The `fields` and `legend` tags

TODO

### Form actions

#### Submitting a form

#### Resetting a form

#### Custom form actions

