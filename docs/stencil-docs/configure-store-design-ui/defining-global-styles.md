# Defining Global Styles



## Configuration file

You can manage your theme's front-end components by configuring its `config.json` file. This file is made up of different keys and values that define your theme's global styles.

`config.json` performs the following functions:

* Provides global context for Stencil's CSS and Handlebars resources.
* Provides values for the Page Builder UI to manage.
* Provides metadata for your theme's listing in the Theme Marketplace.
* Defines variations included in your theme.

For a list of all available keys and values in `config.json`, see [Theme Objects](/theme-objects).

## Requirements and restrictions

The `config.json` file must meet the following requirements:

* It must be named `config.json`, must reside in the root of your `<theme-name>` top-level subdirectory (for example, `/cornerstone/config.json`), and must contain valid JSON.
* The maximum allowable size for a theme's `config.json` file is 64 KB. Exceeding this limit will trigger an error upon uploading the theme to BigCommerce.
* Each key's value is restricted to 64 characters. Exceeding this limit will similarly trigger an upload error.

Apart from the aforementioned size constraints, there is no limit on the number of keys and values you can place in a theme's `config.json`.

<!-- theme: warning -->
> Carefully check your theme against all the listed requirements – including the required keys within the `meta` object and `variations` array. While some requirements are not enforced in local development, they will be validated when you upload your theme to BigCommerce.


### Required config.json keys

The `config.json` file must contain the following keys:

* `name`
* `version`
* `settings` 
* `variations`
* `meta` (see specific requirements below)

### Required meta keys

The `meta` object must contain the following keys:

* `price`
* `author_name`
* `author_email`
* `author_support_url`
* `documentation_url`
* `composed_image`
* `features`

For illustration, below is a commented excerpt.

```json
{
  ...
  "meta": {
    "price": 15000, // in cents; non-negative integer, minimum 0
    "author_name": "eCommerce Themes, Inc.", // Must be a string, not null
    "author_email": "support@example.com", // Must be a string, should be a valid email address, not null
    "author_support_url": "http://example.com/contactus", //  Must be a string, should be a valid URL, not null
    "documentation_url": "http://example.com/guide", // Must be a string, limit of 255 characters, not null
    "composed_image": "path/to/composed.png", // Must be a string path to the composed-image file
    "features": [ // Array of feature strings, all of which must be in the list enumerated here:
          https://github.com/bigcommerce/theme-registry/blob/master/app/schemas/theme_config.json#L33
      "fully_responsive" // Must include at least one feature, and no duplicate entries
    ]
  },
  ...
}
```

### Required variation keys

A theme requires at least one variation. For each variation that you choose to include in your theme, you must provide the following keys:

* `name`
* `id`
* `meta`

For illustration, below is a code sample from Cornerstone.

```json
"variations": [
    {
      "name": "Light",
      "id": "light",
      "meta": {
        "desktop_screenshot": "desktop_light.png",
        "mobile_screenshot": "mobile_light.png",
        "description": "Ideal for a wide range of businesses and brands, this design is fully responsive, simple, and ready for you to add your branding, logo, and products. ...",
    ...
    }
  ]
```
## New products example
The values that you define in the `config.json` file interact with local resources making it possible to customize your theme's appearance globally. Your `config.json` definitions set global defaults for templates, front matter attributes, and Handlebars resources throughout your theme. You can also define custom variables in `config.json`, named according to your needs.
To see how interactions with `config.json` values work, first note the default values in `config.json` for the `homepage_new_products_count` and `product_list_display_mode` keys.
```json
{
"settings": {
  "homepage_new_products_count": 5,
  "product_list_display_mode": "grid",
  }
}
```
Next, open your `templates/pages/home.html` file.
Note the reference to the `homepage_new_products_count` key in the file's front matter. If your current theme's `home.html` front matter omits `products:new:limit`, paste it in for this demonstration.
```html
products:
  new:
    limit: {{theme_settings.homepage_new_products_count}}
  featured:
    limit: {{theme_settings.homepage_featured_products_count}}
  top_sellers:
    limit: {{theme_settings.homepage_top_products_count}}
carousel: {{theme_settings.homepage_show_carousel}}
blog:
    recent_posts:
        limit: {{theme_settings.homepage_blog_posts_count}}
{{#partial "hero"}}
<!-- [...] -->
{{/partial}}
{{> layout/base}}
```
You should see five products displayed in a grid in the New Products section of your homepage.
## Changing page layout using local front matter
In the `home.html` front matter, `products:featured:limit` controls how many products appear on the home page. The number of products is set by the `config.json` file's `homepage_featured_products_count`.
```html
<!-- [...] -->
products:
  featured:
      limit: {{theme_settings.homepage_featured_products_count}}
<!-- [...] -->
```
To set theme-wide configurations in the front matter using `config.json`, replace the `{{theme_settings.homepage_featured_products_count}}` statement in `home.html` with a hard-coded `2`. For reference, see the following example.
```html
products:
  <!-- [...] -->
  featured:
      limit: 2
  <!-- [...] -->
<!-- [...] -->
{{#partial "page"}}
<!-- [...] -->
<div class="main full">
  {{#if products.featured}}
      {{> components/products/featured products=products.featured columns=2}}
  {{/if}}
</div>
{{/partial}}
{{> layout/base}}
```
Reload the page. You should see the number of products displayed in Featured Products change to two.
## Retrieving specific config.json values through Sass
In `config.json`, global variables bring dynamic values into the framework. Sass imports these global variables' values to handle data, such as colors hexadecimal values, and to make the data available to Page Builder.
The following is a snippet of color variables from `config.json`.
```css
{
<!-- [...] -->
"color-highlight": "#00abc9",
"color-highlightDark": "#f2f2f2",
"color-highlightDarker": "#dfdfdf",
<!-- [...] -->
}
```
Below are the corresponding references in the theme's `assets/scss/settings/global/color/_color.scss` file.
```scss
// ...
$color-highlight:       stencilColor("color-highlight");
$color-highlightDarker: stencilColor("color-highlightDarker");
$color-highlightDark:   stencilColor("color-highlightDark");
// ...
```
In `config.json`, try redefining one or more color variables to hex values of your choice. Reload the page to see the effects.
## Adding and removing components
The properties that Stencil abstracts as Handlebars resources are portable between HTML files. To see this in action, open any storefront page in a browser and navigate to the page's footer where you should see the Categories section.
Open your theme's `footer.html` file located in `templates/components/common/footer.html`, navigate to the `footer-info` section, and remove the following `footer-categories` component:
```html
<article class="footer-info-col footer-info-col--small" data-section-type="footer-categories">
  <h3 class="footer-info-heading">{{lang 'footer.categories'}}</h3>
  <ul class="footer-info-list">
      {{#each categories}}
      <li>
          <a href="{{url}}">{{name}}</a>
      </li>
      {{/each}}
  </ul>
</article>
```
Refresh the page. The Categories section should disappear from the footer.
Next, add a `footer` directory to `templates/components` and create a new `categories.html` template (for example, `templates/components/footer/categories.html`). Paste the code block from the previous step into `categories.html`.
```html
<article class="footer-info-col footer-info-col--small" data-section-type="footer-categories">
  <h3 class="footer-info-heading">{{lang 'footer.categories'}}</h3>
  <ul class="footer-info-list">
      {{#each categories}}
      <li>
          <a href="{{url}}">{{name}}</a>
      </li>
      {{/each}}
  </ul>
</article>
```
In `templates/components/common/footer.html`, add a reference to the new template as shown below.
```html
<footer class="footer" role="contentinfo">
  <div class="container">
      <section class="footer-info">
          <!-- [...] -->
          {{> components/footer/categories}}
          <!-- [...] -->
      </section>
  </div>
</footer>
```
Refresh the page. The Categories list should reappear in the footer.
## Resources
### Additional resources
- [Customizing a Theme Video](//youtube.com/watch?v=VZYZsDoEOpQ) (YouTube)
