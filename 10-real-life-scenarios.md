# Real life scenarios

The following examples are created without external dependencies.

## Creating a multistep form

Multistep forms are common on the web. It's used to breakdown a big form into multiple smaller ones. Typically; a multistep form consists of:

1. A navigator indicating which step you're in
2. A way to navigate between steps (forward and backward)
3. Parts of the form displayed and hidden as needed

Let's start with the basic HTML

```html
<form>
  <fieldset>
    <legend>Personal Information</legend>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required />
    <label for="date-of-birth">Date of birth</label>
    <input type="date" id="date-of-birth" name="date-of-birth" required />
  </fieldset>
  <fieldset>
    <legend>Contact Info</legend>
    <label for="tel">Phone number</label>
    <input type="tel" id="tel" name="tel" required />
    <label for="email">Email address</label>
    <input type="email" id="email" name="email" required />
    <fieldset>
      <legend>Preferred contact method</legend>
      <input type="radio" id="method-1" name="preferred-method" value="1" required />
      <label for="method-1">Email</label>
      <input type="radio" id="method-2" name="preferred-method" value="2" required />
      <label for="method-2">Phone</label>
    </fieldset>
  </fieldset>
  <fieldset>
    <legend>Message</legend>
    <label for="message">Message</label>
    <textarea id="message" placeholder="Reason for contact"></textarea>
  </fieldset>
</form>
```

Nothing fancy is going on. We structured our form in a way that each `fieldset` directly descending from the `form` tag will be used as the step. Now let's write down the basic structure for our JavaScript utility that will transform this form into a multistep form

```javascript
class MultiStepForm {
  
  // Will take care of creating the navigation buttons
	setupMultiStepForm() {}
  
  // We will use this to attach all the necessary events listeners
	bindEvents() {}

  // Hide current step and activate next step
  activateNextStep() {}

  // Hide current step and activate previous step
  activatePreviousStep() {}
  
  // Hide current step and activate step by index
  activateStep(stepIndex) {}
  
  // Validate the active step
  validateActiveStep() {}
}
```

We need our class to be reusable to use it across different projects without modifications. To do so, we will need to pass different options to it like the form and the selector for the steps. We should be able to do something similar to the following:

```javascript
const form = new MultiStepForm({
  form: formELement // should be an HTMLFormElement
  stepSelector: 'css-selector-for-the-step'	// String representing a CSS selector
});
```

To do so, we will need to add a `constructor` function to our class:

```javascript
class MultiStepForm {
  /**
    * @param {Object} options
    * @param {HTMLFormElement} options.form
    * @param {string} options.stepSelector
    */
	constructor(options) {
    this.form = this.options.form;
  }
}
```

Now let's do a couple of checks to avoid errors from being thrown if anything went wrong. We should check that the form exists and there is at least two steps in the form.

```javascript
constructor(options) {
  this.form = this.options.form;
  if (!this.form instanceof HTMLFormElement) {
    console.error('Error, the element used is not a form element');
    return;
  }
  this.steps = this.form.querySelectorAll(this.options.stepSelector);
  if (this.steps.length < 2) {
    console.error(`Error, the form should contain at least 2 steps matching the selector ${this.options.stepSelector}`);
    return;
  }
  this.steps = Array.from(this.steps);
  this.setupMultiStepForm();
}
```

`form` and `steps` will be used a lot in our class so we store them as class members. We do the necessary checks to ensure the script will work without troubles. We also log an error in the console for the developer without breaking the application. You can also `throw` an error if you want.

Note that `querySelectorAll` returns a **`NodeList`** which is an array-like object. We convert it to array using `Array.from` because we will need to work with it using Array methods.

Note that we call `setupMultiStepForm` afterward to bootstrap our form.

So far we are able to identify the form and the steps. Let's start with the first function `setupMultiStepForm`. This function should be doing all the initial work we need like:

1. Create the form step navigator
2. Add previous and next buttons
3. Hide all steps except the first one

Let's start by creating the next and previous buttons for each step. Usually these buttons are placed at the end of the step so we will create a container that will host these buttons and append it to each step

```javascript
setupMultiStepForm() {
  this.steps.forEach(step => {
    // Create the buttons container per step
  	const buttonsContainer = document.createElement('div');
    buttonsContainer.classList.add('buttons-container');
    
    // Create next button
		const nextButton = document.createElement('button');
    // We set the type to button to prevent it from submitting the form
    nextButton.type = 'button';
    // This action will be used to determine the behaviour of the button
    nextButton.dataset.action = 'activateNextStep';
    nextButton.innerText = 'Next';
    
    // Create previous button
    const prevButton = document.createElement('button');
    prevButton.type = 'button';
    prevButton.dataset.action = 'activatePrevStep';
    prevButton.innerText = 'Next';
    
    // Append buttons to the container
    buttonContainer.appendChild(prevButton);
    buttonContainer.appendChild(nextButton);
    // Append container to the step
    step.appendChild(buttonContainer);
  });
}
```

Now each step should have a new child containing previous and next buttons. The problem now is, not all steps should have a previous and a next button. The first step shouldn't have a previous button and the last step shouldn't have a next button. We can do that by modifying our script as follow:

```javascript
setupMultiStepForm() {
  this.steps.forEach((step, index, array) => {
    // Create the buttons container per step
  	const buttonsContainer = document.createElement('div');
    buttonsContainer.classList.add('buttons-container');
    
    // Temporary placeholder for buttons;
    const buttons = [];
    
    // If it isn't the first step, create a previous button
    if (index !== 0) {
      // Create previous button
      const prevButton = document.createElement('button');
      prevButton.type = 'button';
      prevButton.dataset.action = 'prevStep';
      prevButton.innerText = 'Previous';
      buttons.push(prevButton);
    }
    
    // If it isn't the last step, create a next button
    if (index === array.length - 1) {
      // Create next button
      const nextButton = document.createElement('button');
      // We set the type to button to prevent it from submitting the form
      nextButton.type = 'button';
      // This action will be used to determine the behaviour of the button
      nextButton.dataset.action = 'nextStep';
      nextButton.innerText = 'Next';
      buttons.push(nextButton);
    }
    // Append buttons to the container
    buttons.forEach(button => {
	    buttonContainer.appendChild(button);
    });
    // Append container to the step
    step.appendChild(buttonContainer);
  });
}
```

Now each step should have the correct navigation buttons. These buttons will do nothing 

Next we should hide all steps except the first one. We can do that by adding the following line to the loop:

```javascript
setupMultiStepForm() {
  this.steps.forEach((step, index, array) => {
    ...
    // If it isn't the first step, create a previous button
    if (index !== 0) {
      ...
      step.hidden = true;
    }
    ...
  });
}
```

`hidden` is a global boolean attribute that is used to indicate that an element is no longer relevant on the document. Browsers will hide elements with `hidden` attribute.

Note that CSS can easily override the display elements with `hidden` attribute because it overrides the user agent stylesheet. For example if you're setting the step container's display using CSS to `block` or `flex`, it will be visible. The best way to ensure that an element with `hidden` is to use CSS like the following:

```css
[hidden] {
  display: none !important;
}
```

