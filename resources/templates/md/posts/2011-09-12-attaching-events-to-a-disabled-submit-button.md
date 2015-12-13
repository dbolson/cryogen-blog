{:title "Attaching Events to a Disabled Submit Button"
:layout :post
:tags ["javascript"]}

There was a form that had a few required fields, and I wanted to show a message when the user
hovered over the submit button when not every field was completed. The problem was that the submit
button was disabled until the fields are filled in, and I couldn't attach an event to a disabled
form element.

One solution is to add an invisible element over the button.

```javascript
var $disabledSubmit = $('#submit_wrapper input:disabled');
var $disabledSubmitParent = $('#submit_wrapper');
var $overlay = $('<div />');
$overlay.css({
  position: 'absolute',
  top: $disabledSubmit.position().top,
  left: $disabledSubmit.position().left,
  width: $disabledSubmit.outerWidth(),
  height: $disabledSubmit.outerHeight(),
  zIndex: 10,
  opacity: 0
});
$overlay.mouseover(this.submitHoverOver);
$overlay.mouseout(this.submitHoverOut);
$disabledSubmitParent.append($overlay);
```

This created an overlay over the button that handled the hiding and showing (`submitHoverOver()`/`submitHoverOut()`)
of the message.

When the form was ready to submit and the button was enabled, we needed to do two things. The
first was to lower the z-index of the overlay so the user can access the button.

```javascript
$overlay.css('z-index', -1);
```

The second was to unbind the events on the overlay so we didn't continue to show the message.

```javascript
$overlay.unbind();
```

If the user changed the data to be in a bad state, we disabled the submit button. We also needed
to reattach the events and crank up the z-index of the overlay.

```javascript
$overlay.css('z-index', 10);
$overlay.mouseover(this.submitHoverOver);
$overlay.mouseover(this.submitHoverOut);
```

Now, instead of putting up an error message or more text about required fields, the user would be
directed to finish the form if he hadn't done so when he tried to submit.
