# HTML

# Table of contents

- [HTML](#html)
  - [Introduction to HTML5 Elements](#introduction-to-html5-elements)
  - [Add Images to Your Website](#add-images-to-your-website)
  - [Link to External Pages with Anchor Elements](#link-to-external-pages-with-anchor-elements)
    - [Link to Internal Sections of a Page with Anchor Elements](#link-to-internal-sections-of-a-page-with-anchor-elements)
  - [Create a Form Element](#create-a-form-element)
    - [Add a Submit Button to a Form](#add-a-submit-button-to-a-form)
    - [Use HTML5 to Require a Field](#use-html5-to-require-a-field)

## Introduction to HTML5 Elements

HTML5 introduces more descriptive HTML tags. These include `main`, `header`, `footer`, `nav`, `video`, `article`, `section` and others.

These tags give a descriptive structure to your HTML, make your HTML easier to read, and help with Search Engine Optimization (SEO) and accessibility. The `main` HTML5 tag helps search engines and other developers find the main content of your page.

Example usage, a `main` element with two child elements nested inside it:

```html
<main> 
  <h1>Hello World</h1>
  <p>Hello Paragraph</p>
</main>
```

**Note:** Many of the new HTML5 tags and their benefits are covered in the Applied Accessibility section.

## Add Images to Your Website

You can add images to your website by using the `img` element, and point to a specific image's URL using the `src` attribute.

An example of this would be:

```html
<img src="https://www.freecatphotoapp.com/your-image.jpg">
```

Note that `img` elements are self-closing.

All `img` elements **must** have an `alt` attribute. The text inside an `alt` attribute is used for screen readers to improve accessibility and is displayed if the image fails to load.

**Note:** If the image is purely decorative, using an empty `alt` attribute is a best practice.

Ideally the `alt` attribute should not contain special characters unless needed.

Let's add an `alt` attribute to our `img` example above:

```html
<img src="https://www.freecatphotoapp.com/your-image.jpg" alt="A business cat wearing a necktie.">
```

## Link to External Pages with Anchor Elements

You can use `a` (*anchor*) elements to link to content outside of your web page.

`a` elements need a destination web address called an `href` attribute. They also need anchor text. Here's an example:

```html
<a href="https://www.freecodecamp.org">this links to freecodecamp.org</a>
```

Then your browser will display the text `this links to freecodecamp.org` as a link you can click. And that link will take you to the web address `https://www.freecodecamp.org`.

### Link to Internal Sections of a Page with Anchor Elements

`a` (*anchor*) elements can also be used to create internal links to jump to different sections within a webpage.

To create an internal link, you assign a link's `href` attribute to a hash symbol `#` plus the value of the `id` attribute for the element that you want to internally link to, usually further down the page. You then need to add the same `id` attribute to the element you are linking to. An `id` is an attribute that uniquely describes an element.

Below is an example of an internal anchor link and its target element:

```html
<a href="#contacts-header">Contacts</a>
...
<h2 id="contacts-header">Contacts</h2>
```

When users click the `Contacts` link, they'll be taken to the section of the webpage with the **Contacts** header element.

## Create a Form Element

You can build web forms that actually submit data to a server using nothing more than pure HTML. You can do this by specifying an `action` attribute on your `form` element.

For example:

```html
<form action="/url-where-you-want-to-submit-form-data">
  <input>
</form>
```

### Add a Submit Button to a Form

Let's add a `submit` button to your form. Clicking this button will send the data from your form to the URL you specified with your form's `action` attribute.

Here's an example submit button:

```html
<button type="submit">this button submits the form</button>
```

### Use HTML5 to Require a Field

You can require specific form fields so that your user will not be able to submit your form until he or she has filled them out.

For example, if you wanted to make a text input field required, you can just add the attribute `required` within your `input` element, like this: `<input type="text" required>`
