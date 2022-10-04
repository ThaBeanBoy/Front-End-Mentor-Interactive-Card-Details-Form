# Frontend Mentor - Interactive card details form solution

This is a solution to the [Interactive card details form challenge on Frontend Mentor](https://www.frontendmentor.io/challenges/interactive-card-details-form-XpS8cKZDWw). Frontend Mentor challenges help you improve your coding skills by building realistic projects.

## Table of contents

- [Frontend Mentor - Interactive card details form solution](#frontend-mentor---interactive-card-details-form-solution)
  - [Table of contents](#table-of-contents)
  - [Overview](#overview)
    - [The challenge](#the-challenge)
    - [Screenshot](#screenshot)
    - [Links](#links)
  - [My process](#my-process)
    - [Built with](#built-with)
    - [What I learned](#what-i-learned)
    - [Continued development](#continued-development)
    - [Useful resources](#useful-resources)
  - [Author](#author)

## Overview

### The challenge

Users should be able to:

- Fill in the form and see the card details update in real-time
- Receive error messages when the form is submitted if:
  - Any input field is empty
  - The card number, expiry date, or CVC fields are in the wrong format
- View the optimal layout depending on their device's screen size
- See hover, active, and focus states for interactive elements on the page

### Screenshot

![](./screenshot.png)

### Links

- [Repo](https://github.com/ThaBeanBoy/Front-End-Mentor-Interactive-Card-Details-Form)
- [Live Site](https://thabeanboy.github.io/Front-End-Mentor-Interactive-Card-Details-Form/)

## My process

### Built with

- Semantic HTML5 markup
- CSS custom properties
- Flexbox
- CSS Grid
- Desktop-first workflow

### What I learned

Throughout the development of this project, one thing that never occured to me was that you could use querySelector with any html object in Js. This made things easier in when working with the DOM.

```js
const field = document.querySelector(`[data-field-for="${fieldName}"]`);
const inputElement = field.getElementsByTagName('input');
```

When trying to perform form validation, the amount of code I wrote eventually became too cumbersome. What was interesting was that alot of that cumbersome code was jast a repetition of the previous code with very little change in it. I decided it would be easier to encapsulate all this logic in a field object that can perform input validation based on the values I pass into it's parameters.

```js
const fieldElements = (fieldName) => {
  const field = document.querySelector(`[data-field-for="${fieldName}"]`);

  return {
    field: field,
    inputs: field.getElementsByTagName('input'),
    err: field.querySelector('.input-err'),
    errMessages: [],

    displayErrors: function () {
      if (this.errMessages.length > 0) {
        //Deleting old messages
        this.err.innerHTML = '';

        // Displaying the current err messages
        this.errMessages.forEach(
          (msg) => (this.err.innerHTML += `${msg} <br />`)
        );
        this.field.setAttribute('data-err', '');

        // highlighting input
        Array.from(this.inputs).forEach(
          (input) => (input.style.borderColor = errColor)
        );
      } else {
        // There are no errors
        this.field.removeAttribute('data-err');

        // remove coloring
        Array.from(this.inputs).forEach(
          (input) => (input.style.borderColor = normalBorderColor)
        );
      }
    },

    validation: function (validationCase, errMessage, highjackDisplay) {
      if (validationCase) {
        this.errMessages.push(errMessage);
      } else {
        indexOfErrMessage = this.errMessages.indexOf(errMessage);
        if (indexOfErrMessage >= 0) {
          this.errMessages.splice(this.errMessages.indexOf(errMessage), 1);
        }
      }

      if (highjackDisplay === undefined) {
        this.displayErrors();
      } else {
        highjackDisplay();
      }
    },
  };
};
```

Throug the use of the objects, validating the form was easier. The only issue was the Exp. Date (MM/YY) inputs, if one input field from field has an error, both the inputs' border color are changed to the error color.

```js
CardNumberInput = fieldElements('card-number').inputs[0];
CardNumberInput.addEventListener('input', () => {
  spacerLengthKeys = [4, 9, 14];
  CardNumberInput.value += spacerLengthKeys.some(
    (length) => length === CardNumberInput.value.length
  )
    ? ' '
    : '';
});

const errColor = 'hsl(0, 100%, 66%)';
const normalBorderColor = '#8e8593';

document.querySelector('#confirm-btn').addEventListener('click', () => {
  event.preventDefault();

  const errBlankStatement = "Can't be blank";
  const errNumbersOnly = 'Wrong format, numbers only';

  // Checking card holder's name
  const cardHolderName = fieldElements('card-holder-name');

  cardHolderName.validation(
    cardHolderName.inputs[0].value.trim() === '',
    errBlankStatement
  );

  // Checking errors in card number input box
  const accnNumberField = fieldElements('card-number');

  accnNumberField.validation(
    accnNumberField.inputs[0].value.trim() === '',
    errBlankStatement
  );

  accnNumberField.validation(
    /[a-zA-Z]/g.test(accnNumberField.inputs[0].value.trim()),
    errNumbersOnly
  );

  // Checking errors in Exp Date input boxes
  const extraNumbers = fieldElements('exp-date');

  extraNumbers.validation(
    extraNumbers.inputs[0].value.trim() === '' ||
      extraNumbers.inputs[1].value.trim() === '',
    errBlankStatement
  );

  extraNumbers.validation(
    /[a-zA-Z]/g.test(extraNumbers.inputs[0].value.trim()) ||
      /[a-zA-Z]/g.test(extraNumbers.inputs[1].value.trim()),
    errNumbersOnly
  );

  if (extraNumbers.errMessages.length > 0) {
    extraNumbers.err.innerHTML = '';
    extraNumbers.errMessages.forEach(
      (msg) => (extraNumbers.err.innerHTML += `${msg} <br />`)
    );
    extraNumbers.field.setAttribute('data-err', '');
  } else {
    extraNumbers.err.innerHTML = '';
    extraNumbers.field.removeAttribute('data-err');
  }
  console.log(extraNumbers.errMessages);
  // Checking CVC input
  const cvcField = fieldElements('cvc');

  cvcField.validation(
    cvcField.inputs[0].value.trim() === '',
    errBlankStatement
  );

  cvcField.validation(
    /[a-zA-Z]/g.test(cvcField.inputs[0].value.trim()),
    errNumbersOnly
  );

  // Display the complete status
  const noErrorsInFields = [
    cardHolderName,
    accnNumberField,
    extraNumbers,
    cvcField,
  ]
    .map((field) => field.errMessages.length > 0)
    .every((bool) => bool === false);

  if (noErrorsInFields) {
    const form = document.querySelector('form');
    const completeStatus = document.querySelector('.complete-state');

    form.style.display = 'none';
    completeStatus.style.display = 'flex';
  }
});
```

A cool thing I like doing is using array methods. To detect if the were no errors in the form, I had to do the following :

1. Put all the field objects in an array
2. Map the array to an array of booleans, these booleans are a reflection of the number of error messages in the error messages array. If the number of error messages is greater than 0, that means that there are errors.
3. If every boolean value from the array is a false, that means that there were no errors, & the succesful complete state con be shown.

```js
const noErrorsInFields = [
  cardHolderName,
  accnNumberField,
  extraNumbers,
  cvcField,
]
  .map((field) => field.errMessages.length > 0)
  .every((bool) => bool === false);

if (noErrorsInFields) {
  // Done state can be showed
}
```

### Continued development

One thing I would love to improve upon is using HTML Lnadmark tags, like [@AdrianoEscarabote]() suggested in a previous project.

### Useful resources

- [Coolors Gradient Maker](https://coolors.co/gradient-maker/f1e1c2-fcbc98) - This helped me make gradients.

## Author

- Website - [Add your name here](https://www.your-site.com)
- Frontend Mentor - [@yourusername](https://www.frontendmentor.io/profile/yourusername)
- Twitter - [@yourusername](https://www.twitter.com/yourusername)
