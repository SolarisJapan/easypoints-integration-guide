# EasyPoints Integration Guide

## App Setup

#### Install the EasyPoints app

The EasyPoints app can be found on the Shopify app store here: https://apps.shopify.com/easy-points

Installing the app will automatically insert liquid, css, and javascript files into the theme which can be used to aid in the integration. Not all of the files need to be used.

#### Select custom integration

There will be a step during the onboarding process when selecting between the widget and the custom integration. Select custom integration. This will halt your progress through the onboarding. This is to avoid the merchant being charged before their integration is completed and the app functional. Once the integration is finished please request complete access to the app at team@lunaris.jp.

## Liquid Integration

#### Load EasyPoints Redemption Form Liquid

Ensure that the content within "snippets/redemption_form.liquid" is loaded.

```
{% include 'redemption_form', hide_points_form: true %}
```

This snippet includes inputs, so it should be loaded outside any form elements on the page except for your EasyPoints redemption form. These inputs are hidden when given the above options. This is the recommended setup. The contents of the snippet can be turned into a form and submitted via JavaScript.

#### Redemption Form Submission

The recommended method of submission is to create an html input element with the id `shown-point-value`. The value field of this element should be input by the user as the number of points they wish to redeem. A function to update the virtual form included in the previous step and submit is found in "assets/easy_points.js".

```
submitRedemptionForm()
```

This function will also call `pointRedemptionValidation` and depending upon the result will add the html class "invalid" to `input#shown-point-value`. The css of `#invalid` can be customized to fit the theme.


## Snippets

These are useful code segments for integrating with the EasyPoints JavaScript and script tags. Typically content within any spans will be overwritten, so the inside values should be viewed as defaults / placeholders.

##### Applied Discount

Include anywhere that should display the currently applied discount.

```
<span data-loyal-target="applied-discount">0</span>
```

##### Point Balance

Include anywhere that should display the user's current point balance.

```
<span data-loyal-target="balance">-</span>
```

##### Item Point Value

Include anywhere that should display the point value of an item / product / variant. Insert the correct liquid variable as the value for `data-loyal-currency-cost`. Only the inner span's contents are overwritten.

```
<span data-loyal-target="point-value" data-loyal-currency-cost="{{ product.price }}">
  <span data-loyal-target="point-value-location">-</span>
</span>
```

##### Item Point Value

Include anywhere that contains content which should be hidden when the app is in stealth mode.

```
{% if shop.metafields.loyalty['stealth_mode'] == "false" %}
  ...
{% endif %}
```

##### Example

```
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
