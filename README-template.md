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
  - [Acknowledgments](#acknowledgments)

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

If you want more help with writing markdown, we'd recommend checking out [The Markdown Guide](https://www.markdownguide.org/) to learn more.

**Note: Delete this note and the content within this section and replace with your own learnings.**

### Continued development

Use this section to outline areas that you want to continue focusing on in future projects. These could be concepts you're still not completely comfortable with or techniques you found useful that you want to refine and perfect.

**Note: Delete this note and the content within this section and replace with your own plans for continued development.**

### Useful resources

- [Example resource 1](https://www.example.com) - This helped me for XYZ reason. I really liked this pattern and will use it going forward.
- [Example resource 2](https://www.example.com) - This is an amazing article which helped me finally understand XYZ. I'd recommend it to anyone still learning this concept.

**Note: Delete this note and replace the list above with resources that helped you during the challenge. These could come in handy for anyone viewing your solution or for yourself when you look back on this project in the future.**

## Author

- Website - [Add your name here](https://www.your-site.com)
- Frontend Mentor - [@yourusername](https://www.frontendmentor.io/profile/yourusername)
- Twitter - [@yourusername](https://www.twitter.com/yourusername)

**Note: Delete this note and add/remove/edit lines above based on what links you'd like to share.**

## Acknowledgments

This is where you can give a hat tip to anyone who helped you out on this project. Perhaps you worked in a team or got some inspiration from someone else's solution. This is the perfect place to give them some credit.

**Note: Delete this note and edit this section's content as necessary. If you completed this challenge by yourself, feel free to delete this section entirely.**
