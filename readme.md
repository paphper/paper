# Paphper

Paphper is Static Site Generator written on top of ReactPHP.

### Installation

`composer create-project paphper/paper`

### Development

`php paper dev`

This runs a development server at `http://localhost:8888`. 
This also looks for changes in the files in the `pages` folder and reflects in the browser.
> Note: This does not watch for the changes in the `layouts`. So, if you make a change in the layout, 
you will have to modify something in your page to see the changes in the browser.
You can change this port to your choice at [/config.php](/config.php).

### Folder Structure

#### /pages
This is where all the pages for the site lives. `.html`, `.md` and [Blade](https://laravel.com/docs/7.x/blade) files are supported as pages. The folder structure of pages decides the routes.    
For example:
- `pages/index.html` will be displayed at `/`
- `pages/readme.md` will be displayed at `/readme`
- `pages/posts/post.html` will be displayed at `/posts/post`
- `pages/blade.blade.php` will be displayed at `/blade`

#### /layouts
This is where all the layouts for the site lives. Only html files are supported as layouts.

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

### File types supported

Paphper supports three file types for now.

1. Html
1. Md
1. [Blade Templates](https://laravel.com/docs/7.x/blade)

### Image resize support

Paphper has support for image resizes as well from a single source file. You can reference size of an image and it will generate that size. 
For example:  
There is a file [envelope.jpg](/assets/envelope.jpeg) in assets folder. It is referenced in the [index.html](/pages/index.html) 
and in referenced as `/envelope_50x50.jpeg` in [index layout](/layouts/index.html). This will copy the original file and also create a /envelope_50x50.jpeg in the given dimension.  

For this to work. The image MUST be referenced as fiiname_widthxheight.jpeg.
For example:  

To generate a 50x50 dimension image for `/envelope.jpeg`, it must be referenced as `/envelope50x50.jpeg`.

### Building

`php paper build` builds html files into the build folder. Which can be specified [here](/config.php).
![img](https://i.ibb.co/qrrzYrJ/Screen-Shot-2020-05-18-at-10-25-34-PM.png)


## Advance Usage

I have tried to make this thing as flexible and extendable as it can be. Let me explain a bit of architecture to help me explain this.

### Architecture

#### PageResolvers

PageResolver is a simple class that implements [PageResolverInterface.php](https://github.com/paphper/core/blob/master/src/Contracts/PageResolverInterface.php)

This is the source of the route structure. The functions explained.

1. `getPages` should return what pages are to be build. It should be a Promise that resolver to an array of strings.(`pages`).
1. `getBuildFilename(string $page)` should return the file that this should be added the parsed content of the page to. MUST be a full path
1. `getBuildFolder` should return the folder that should be created to place the file. MUST be a full path.  

The default PageResolver used in Paphper is [FilesystemPageResolver](https://github.com/paphper/core/blob/master/src/PageResolvers/FilesystemPageResolver.php)
This scans the folder in the pages folder and retrieves that pages and decides on the files and folder to be created.

This can be extended to use, for instance, a mysql table with posts and create the page based on the uri of that post.

### FileTypeResolver

Content is the class that interprets any file into HTML. It implements [Contentinterface](https://github.com/paphper/core/blob/master/src/Contracts/ContentInterface.php).
The needs to have one function.

1. `resolveFileType(string $filename)` when passes a filename should simple return a `ContentInterface`. 
`ContentInterface` returns promise that resolves to a string. This is the final content that will be written in the HTML file generated.

We have three FileTypeResolvers at the moment.

1. HtmlResolver
1. MdResolver
3. BladeResolver

Paphper uses this interface to internally add support for multiple file types.

### MetaInterface

Meta parser parses the meta in a page. The default meta parser is [Paper Tag Parser](https://github.com/paphper/core/blob/master/src/Parsers/AbstractPaperTagParser.php) which implements [MetaInterfaec](https://github.com/paphper/core/blob/master/src/Contracts/MetaInterface.php)
`Paper Tag Parser` as name suggests the `<paper>` tag on top of the files.

You can implement your own meta parser as well. The functions explained.

1. `getBody` this should be able to get the body of the page without the meta that will be written to the HTML.
1. `getLayout` this should be able to get the layout that will be used. In case of blade templates, this is not required because it is handled by blade itself.
1. `getLayoutContent` should get the content of the layout so that the meta and content could be passed to this.
1. `get` this should return meta value on passing the meta key
1. `getExtraMetas` this is all the metas
1. `process` returns a promise. Since we are working with promises for filesystem, the file has to be read once to process content of the page. 
If we process promises individually in every call it becomes hard to return as types and becomes a callback hell. 
Hence we call the process function to set all the properties.

### Putting this together

We basically put this together to create a static site. 

https://github.com/paphper/core/blob/master/paper#L26-L43. These are the defaults used in `paper`. You can customize and write you own. I am planning to add more. 
Let me know what should be added to this. Create a PR or issues for suggestions, contributions and feedback.



