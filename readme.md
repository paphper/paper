# Paper PHP

Paper PHP is Static Site Generator written on top of ReactPHP.

### Installation

`composer create-project paphper/paper`

### Development

`php paper dev`

This runs a development server at `http://localhost:8888`. You can change this port to your choice at [/config.php](/config.php).

### Folder Structure

#### /pages
This is where all the pages for the site lives

#### /layouts
This is where all the layouts for the site lives

#### /assets
This is where the images, css and other assets live

Note: All of these can be changed here in the [/config.php](/config.php).

### `<paper>`
We include 'meta' for post in a `<paper>` tag. Meta here means whatever you want to be replaced in the content of the page.

> `{content}` in the layout and `layout` in the `<paper>` tag are reserved and cannot be used to replace the contents in the page

The syntax in the template is `{meta}`.
For example if you want to add title to a page `{title}`, add a 
```
title: This is the test title
```
in your paper tag. 
and in your layout. You would add

```html
...
<title>{title}</title>
...

```

This will generate 
```html
<title>This is the test title</title>
```
in the HTML.
  
Example paper tag
```
<paper>       
    description: this is the best description
    title: This is the test title
    layout: blog.html
</paper>
``` 
