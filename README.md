# Vue.js hCaptcha Component Library

Forked from hCaptcha Component Library for Vue.js, compatible with Vue 2+.

## What's different

Official hCaptcha doesn't fit most of the screens: Crome for Android, laptop (1366x768), etc which is ugly when page is designed with custom scroll behavior. There's no options to customize hCaptcha, so here's a little hacky way to adjust look and feel of hcapthca's challenge popup.

## Installation
You can install this library via npm with:
```
npm install atomminer/vue-hcaptcha --save
```

or by including the library in a script tag
```
<script src="../vue-hcaptcha.js"></script>
```

#### Basic Usage
```
<template>
    <vue-hcaptcha sitekey="**Your sitekey here**"></vue-hcaptcha>
</template>

<script>
  import VueHcaptcha from 'vue-hcaptcha';
  export default {
    ...
    components: { VueHcaptcha }
  };
</script>
```

The component will automatically include and load the hCaptcha API library and append it to the root component.
This is designed for ease of use with the hCaptcha API!

**Note**: There's a known flaw when there are multiple captchas being rendered. It's recommended to use only one captcha per page.

#### Customizing captcha's popup

To address the problem of hCaptha being larger than visible screen, here's an example of scaling popup to 75% without breaking any functionality:

```
<template>
    <vue-hcaptcha sitekey="**Your sitekey here**" popupclass="am-captcha-wrapper"></vue-hcaptcha>
</template>

<script>
  import VueHcaptcha from 'vue-hcaptcha';
  export default {
    ...
    components: { VueHcaptcha }
  };
</script>
```

and CSS:

```
/* start hcaptcha hack */
.am-captcha-wrapper {
	transform:scale(0.75);
	-webkit-transform:scale(0.75);
	transform-origin:0 0;
	-webkit-transform-origin:0 0;
}
/* scale hcaptcha's backdrop to fill the screen */
.am-captcha-wrapper > div:nth-child(2) {
	left: -300%!important;
	top: -300%!important;
	width: 600%!important;
	height: 600%!important;
}
/* end hcaptcha hack */
```

### Api

#### Props

|Name|Values/Type|Required|Description|
|---|---|---|:---:|
|`sitekey`|String|**Yes**|This is your sitekey, this allows you to load captcha. If you need a sitekey, please visit [hCaptcha](https://www.hcaptcha.com), and sign up to get your sitekey.|
|`size`|String (normal, compact, invisible)|No, default: normal|This specifies the "size" of the component. hCaptcha allows you to decide how big the component will appear on render, this always defaults to normal.|
|`theme`: String (light, dark)|No, default: light|hCaptcha supports both a light and dark theme. If no theme is inherently set, the captcha will always default to light.|
|`tabindex`|Integer|No, default: 0|Set the tabindex of the widget and popup. When appropriate, this can make navigation of your site more intuitive.|
|`popupclass`: String|No, default: ''|CSS class to apply to hcaptcha popup wrapper. If no class specified, this component will behave exactly like the official one|

#### Events

- `@verify="onVerify"`
- `@expired="onExpired"`
- `@error="onError"` (The captcha will automatically reset on error)

