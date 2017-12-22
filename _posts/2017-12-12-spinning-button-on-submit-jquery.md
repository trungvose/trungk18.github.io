---
title: "Spinning button onSubmit form with jQuery and ASP.NET MVC"
categories: javascript jquery mvc
---

### TL;DR: See the demo

<p data-height="265" data-theme-id="0" data-slug-hash="MrWrNw" data-default-tab="js,result" data-user="trungk18" data-embed-version="2" data-pen-title="Spinning button onSubmit form with jQuery" class="codepen">See the Pen <a href="https://codepen.io/trungk18/pen/MrWrNw/">Spinning button onSubmit form with jQuery</a> by Vo Tuan Trung (<a href="https://codepen.io/trungk18">@trungk18</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script src="https://production-assets.codepen.io/assets/embed/ei.js"> </script>

------

In Zyllem, we are leveraging ASP.NET MVC 5 to build our product. So basically, almost 80% of our code base and UI stuff is doing as server side. 20% of the code is the client-side app written in JavaScript in AngularJS, and also TypeScript in Angular 2/4.

There is a traditional UX issue is when we submit the form, sometimes the server takes a long time to respond to the client so that the page stays the same. Most of the time, what we do is to try to click the submit button again, and again. In few last sprint, my boss and I introduced the spinner button.

1. We add an attribute `data-spinning-button` to the button with type submit in every form.
2. In jQuery, we find the parents of the button that match a form and try to validate the form.
3. If the form is valid, change the button content to be a spinner with a Fontawesome icon.
4. Because it is the server side code, so every time we finished proceeding on the server the whole view will be rendered again. It is same If there is any error, so we don't have to worry about If the error happens.

The purpose is to inform user that the system is proceeding their request and also prevent them from submitting the form multiple times by disabling the button.

The sample code in MVC looks like.

```html
@using (var f = Html.Bootstrap().Begin(new Form().LabelWidthMd(4)))
{
    @Html.Bootstrap().TextBoxFor(x => x.UserName).Placeholder("Email").ShowValidationMessage(true)
    @Html.Bootstrap().PasswordFor(x => x.Password).Placeholder("Password").ShowValidationMessage(true)

    <button type="submit" class="btn btn-primary btn-block" data-spinning-button>
        Log in
    </button>
}
```

```javascript
var zyllemMain = (function(){
  function processSubmitLoader() {
    $('button[data-spinning-button]').click(function () {
        var $this = $(this);
        let formId = $this.data('spinning-button');
        let $form = formId ? $('#' + formId) : $this.parents('form');
        if ($form.length) {
            //form.valid() will be applicable If you are using jQuery validate https://jqueryvalidation.org/
            //asp.net mvc used it by default with jQuery Unobtrusive Validation
            if ($form.valid()) {
                $this.html("<i class='fa fa-circle-o-notch fa-spin'></i>").attr("disabled", "")
                $form.submit();
            }
        }
    })
  }
  return {
    initSpinnerButton: processSubmitLoader
  }
})();

$(document).ready(function(){
  zyllemMain.initSpinnerButton();
});
```