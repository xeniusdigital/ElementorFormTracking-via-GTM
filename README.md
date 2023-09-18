# ElementorFormTracking-via-GTM
Elementor Form Tracking via Google Tag Manager and GA4

#1. Custom HTML tag with Polyfill
A polyfill works like a workaround. There are some things/features that older browsers do not support (meaning that newer Custom JS will not work). Thanks to polyfills (certain custom codes loaded on a page), older browsers can support more things.

In this case, we’ll be using an Element.matches() method that is not supported by older browsers. This can be solved by firing a Custom HTML tag with the following code:

<script>
  if (!Element.prototype.matches) {
    Element.prototype.matches = 
        Element.prototype.matchesSelector || 
        Element.prototype.mozMatchesSelector ||
        Element.prototype.msMatchesSelector || 
        Element.prototype.oMatchesSelector || 
        Element.prototype.webkitMatchesSelector ||
        function(s) {
            var matches = (this.document || this.ownerDocument).querySelectorAll(s),
                i = matches.length;
            while (--i >= 0 && matches.item(i) !== this) {}
            return i > -1;            
        };
  }
</script>
![image](https://github.com/xeniusdigital/ElementorFormTracking-via-GTM/assets/5832613/601c3725-f286-4506-b107-4fd5d596b44e)


#2. Utility variable
The next step is to create a Custom JS variable with the following code:

function() {
  return function(target, selector) {
    while (!target.matches(selector) && !target.matches('body')) {
      target = target.parentElement;
    }
    return target.matches(selector) ? target : undefined;
  }
}
Name it cjs find closest. It’s really important to name the variable exactly in that way because we’ll use it in Step #3 (case-sensitive).


#3. Variable that returns the ID of the Elementor Form
And the final step is to create a variable that returns the Elementor form ID after the “Thank you” message was displayed on a screen.

function() {
  var el = {{cjs find closest}}({{Click Element}}, 'form.elementor-form');
  return typeof el !== 'undefined' ? el.id : undefined;
}
Name the variable cjs elementor form id.

For this code to work, your setup must meet 3 requirements:

Utility variable (from the previous chapter) must be named cjs find closest (case-sensitive) because that exact name is used in the JS code above
Built-in Click Element variable must be enabled in your container
CSS Selector must pick the correct node, the actual <form> element. In most cases, form.element-form should work (because we’re targeting a form element with a CSS class elementor-form. If it does not work in your case, get familiar with CSS Selectors in the first place.

SOURCE:
https://www.analyticsmania.com/post/track-elementor-form-with-google-tag-manager-and-google-analytics/
