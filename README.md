## wagtail-markdown: Markdown fields and blocks for Wagtail

Tired of annoying rich text editors getting in the way of your content
input?  Wish Wagtail worked more like a wiki?  Well, now it can.

`wagtail-markdown` provides Markdown field support for [Wagtail](https://github.com/torchbox/wagtail/).
Specifically, it provides:

* A `wagtailmarkdown.blocks.MarkdownBlock` for use in streamfields.
* A `wagtailmarkdown.fields.MarkdownField` for use in page models.
* A `wagtailmarkdown.edit_handlers.MarkdownPanel` for use in the editor interface.
* A `markdown` template tag.

The markdown rendered is based on `python-markdown`, but with several
extensions to make it actually useful in Wagtail:

* Tables.
* Code highlighting.
* Inline links to pages (`<:My page name|link title>`) and documents
  (`<:doc:My fancy document.pdf>`), and inline images
  (`<:image:My pretty image.jpeg>`).
* Inline Markdown preview using [SimpleMDE](http://nextstepwebs.github.io/simplemde-markdown-editor/)

These are implemented using the `python-markdown` extension interface.

### Installation
Alpha release is available on Pypi - https://pypi.org/project/wagtail-markdown/ - installable via `pip install wagtail-markdown`. It's not a production ready release.


### Using it

Add it to `INSTALLED_APPS`:

```python
INSTALLED_APPS += [
    'wagtailmarkdown',
]
```

Use it as a `StreamField` block:

```python
from wagtailmarkdown.blocks import MarkdownBlock

class MyStreamBlock(StreamBlock):
    markdown = MarkdownBlock(icon="code")
```

<img src="https://i.imgur.com/4NFcfHd.png" width="728px" alt="">

Or use as a page field:

```python
from wagtailmarkdown.edit_handlers import MarkdownPanel
from wagtailmarkdown.fields import MarkdownField

class MyPage(Page):
    body = MarkdownField()

    content_panels = [
        FieldPanel("title", classname="full title"),
        MarkdownPanel("body"),
    ]
```

And render the content in a template:

```html+django
{% load wagtailmarkdown %}
<article>
{{ self.body|markdown }}
</article>
```

You can pass extensions by name to the filter:

```html+django
{% load wagtailmarkdown %}
<article>
{{ self.body|markdown:"my_extension_package.my_extension_module" }}
</article>
```

The markdown filter sanitizes the output of the underlying markdown processor,
to prevent an untrusted user from passing malicious markup. This may conflict
with some extensions.  When additional extensions are specified, it is possible
to specify what additional HTML must be allowed through. This is done by
defining a module variable `allowed_markup` for extensions specified as
`"module name"`, or a class variable `allowed_markup` for extensions specified
as `"module_name:class_name"`. In either case it is possible to recursively add
to the `bleach` white list for tags, attributes, and styles. For example, adding
```python
allowed_markup = {
  tags : ["sub"],
  attributes : { img : ["srcset"] },
  styles : [ "text-decoration-line" ]
}
```
to the extension module allows subscripts, image source sets, and text decoration (such as
underline or strike through) in addition to the HTML permitted by default.

<img src="https://i.imgur.com/Sj1f4Jh.png" width="728px" alt="">

To enable syntax highlighting please use the Pygments (`pip install Pygments`) library.

NB: The current version was written in about an hour and is probably completely
unsuitable for production use.  Testing, comments and feedback are welcome:
<kevin.howbrook@torchbox.com> (or open a Github issue).


### Roadmap for 0.5

* Set up tests: https://github.com/torchbox/wagtail-markdown/issues/28
