# EasyPoints Integration Guide

## App Setup

### Install the EasyPoints app

The EasyPoints app can be found on the Shopify app store here: <https://apps.shopify.com/easy-points>

Installing the app will automatically insert Liquid, CSS, and JavaScript files into the theme which can be used to aid in the integration. Not all of the files need to be used.

### Select custom integration

There will be a step during the onboarding process when selecting between the widget and the custom integration. Select custom integration. This will halt your progress through the onboarding. This is to avoid the merchant being charged before their integration is completed and the app functional.

**_Note_ you will need to request complete access to the app** to subscribe and begin awarding customers loyalty points once the integration is completed by emailing: team@lunaris.jp.

## Liquid Integration

### Automatically Uploaded Files

**_Note_ that the automatically uploaded theme snippet files should be left unaltered**, as files could be automatically re-uploaded by the app or patched for content in the future. For this reason it is strongly advised that custom integrations create new files that override the content of these default files and only call their functions without changing the functions themselves. Breaking changes to JavaScript functions would not occur without notification.

List of automatically uploaded files:

-   assets/easy_points.scss
-   assets/easy_points.js
-   snippets/redemption_form.liquid
-   snippets/easy_points.liquid
-   snippets/easy_points_widget.liquid

### Load EasyPoints redemption form liquid

Ensure that the content within "snippets/redemption_form.liquid" is loaded.

```html
{% include 'redemption_form', hide_points_form: true %}
```

**_Note_ this snippet should be included outside \<form\> elements** as it contains \<input\> elements which could cause complications with forms. These inputs are hidden when given the above options. This is the recommended setup. The contents of the snippet can be turned into a form and submitted via JavaScript (see the example below).

### Redemption form submission

The recommended method of submission is to create an \<input\> element with the id `shown-point-value`. The value field of this element should be input by the user as the number of points they wish to redeem. A function to update the virtual form included in the previous step and submit is found in "assets/easy_points.js".

```javascript
submitRedemptionForm();
```

This function will also call `pointRedemptionValidation` and depending upon the result will add the HTML class "invalid" to `input#shown-point-value`. The CSS of `#invalid` can be customized to fit the theme.

###### Example

```html
<label for="points_redeemed">Points:</label>
<input name="points_redeemed" id="shown-point-value" />
<button type="button" id="redeem-points-button">Redeem</button>
```

```javascript
$("#redeem-points-button").on("click", function() {
  submitRedemptionForm();
});
```

### Reset form submission

To reset a redemption and cancel a coupon, the following function can be used.

```javascript
submitResetForm();
```

Similar to form submission, this function should just be called by a user action such that their coupon is canceled.

###### Example

```html
<button type="button" id="reset-coupon-button">Reset</button>
```

```javascript
$("#reset-coupon-button").on("click", function() {
  submitResetForm();
});
```

### Default behaviour

If using \<input id="shown-point-value"\>, the applied discount (using `submitRedemptionForm()`) will automatically be inserted as the value after page refresh and the input will be disabled. If the discount is reset (using `submitResetForm()`), then the value and disabled attribute will not be updated.

## Snippets

These are useful code segments for integrating with the EasyPoints JavaScript and script tags. Typically content within any spans will be overwritten, so the inside values should be viewed as defaults / placeholders.

### Applied discount

Include anywhere that should display the currently applied discount.

```html
<span data-loyal-target="applied-discount">0</span>
```

### Point balance

Include anywhere that should display the user's current point balance.

```html
<span data-loyal-target="balance">-</span>
```

### Item point value

Include anywhere that should display the point value of an item / product / variant. Insert the correct liquid variable as the value for `data-loyal-currency-cost`. Only the inner span's contents are overwritten.

```html
<span data-loyal-target="point-value" data-loyal-currency-cost="{{ product.price }}">
  <span data-loyal-target="point-value-location">-</span>
</span>
```

### Stealth mode filter

Include anywhere that contains content which should be hidden when the app is in stealth mode.

```html
{% if shop.metafields.loyalty['stealth_mode'] == "false" %}
  ...
{% endif %}
```

###### Example

```html
{%- for item in cart.items -%}
  <div class="cart-item">
   ...
   {% if shop.metafields.loyalty['stealth_mode'] == "false" and customer %}
      <span data-loyal-target="point-value" data-loyal-currency-cost="{{ item.final_price }}">
        This item awards <span data-loyal-target="point-value-location">-</span> points.
      </span>
    {% endif %}
  </div>
{% endfor %}
```

## Design Guidelines Checklist

These are suggestions for content that your custom EasyPoints integration may want to include visibly on an appropriate page. Some of them should only be visible if the app is out of stealth mode or if a customer is logged in.

-   Customer point balance
-   Item point value (in the product page and cart)
-   Currently applied discount
-   Cart point value (before and after discount)
-   Redemption form (redemption input, redemption button, max redeemable points)
-   Reset form (locked redemption input, reset button)

## Troubleshooting

-   Ensure EasyPoints' app proxy url is set to: "/apps/loyalty"
-   Confirm that EasyPoints' liquid snippet files are included at the end of the \<head\> of "layout/theme.liquid"
-   Confirm that "{% include 'redemption_form' %}" was added to the liquid
-   Ensure your custom files have been included in the theme
