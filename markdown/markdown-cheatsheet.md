# Markdown Cheat Sheet

Thanks for visiting [The Markdown Guide](https://www.markdownguide.org)!

This Markdown cheat sheet provides a quick overview of all the Markdown syntax elements. It can’t cover every edge case, so if you need more information about any of these elements, refer to the reference guides for [basic syntax](https://www.markdownguide.org/basic-syntax) and [extended syntax](https://www.markdownguide.org/extended-syntax).

# Basic Syntax

These are the elements outlined in John Gruber’s original design document. All Markdown applications support these elements.
### Badges
[![](https://img.shields.io/badge/Thing-Value-darkgreen)](http://www.http2demo.io/)

### Heading

# H1
## H2
### H3
#### H4
##### H5
###### H6

Alternatively, for H1 and H2, an underline-ish style:

Alt-H1
======

Alt-H2
------

### Bold

**bold text**

### Italic

*italicized text*

### Blockquote

> blockquote

### Ordered List

1. First item
2. Second item
3. Third item

### Unordered List

- First item
- Second item
- Third item

### Code

`code`

### Code Block with Syntax Highlighting
```python
if var_user is attentive:
  var_user.gets_treat(type='reeses peanut butter cup')
else:
  var_user.gets_a_timeout(duration=3600)

# Works for a variety of languages, but support varies by markdown renderer.
```

### Horizontal Rule

---

# Extended Syntax

These elements extend the basic syntax by adding additional features. Not all Markdown applications support these elements.

### Table

| Syntax | Description |
| ----------- | ----------- |
| Header | Title |
| Paragraph | Text |

### Fenced Code Block

```
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
```

### Footnote

Here's a sentence with a footnote. [^1]

[^1]: This is the footnote.

### Heading ID

### My Great Heading {#custom-id}

### Definition List

term
: definition

### Strikethrough

~~The world is flat.~~

## Task List

- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media

# External References

## Links

[title](https://www.example.com)

## Logos & Images
Here's a logo (hover to see the title text):

Inline-style: 
![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")

Reference-style: 
![alt text][logo]

[logo]: https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 2"


## Badges
https://shields.io/category/build
https://img.shields.io/badge/environment-tst-orange
https://gist.github.com/tterb/982ae14a9307b80117dbf49f624ce0e8

## YouTube Videos

They can't be added directly but you can add an image with a link to the video like this:
```html
<a href="http://www.youtube.com/watch?feature=player_embedded&v=YOUTUBE_VIDEO_ID_HERE
" target="_blank"><img src="http://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>
```
Or, in pure Markdown, but losing the image sizing and border:

```markdown
[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg)](http://www.youtube.com/watch?v=YOUTUBE_VIDEO_ID_HERE)
```

## Attribution
* [The Markdown Guide](https://www.markdownguide.org) is the source of the majority of this content
* [Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) by Adam Pritchard was used to fill some gaps
* <img alt="@epopisces" width="22" height="22" src="https://avatars2.githubusercontent.com/u/17202697?s=60&amp;v=4"> [Lucas Gallagher](https://github.com/epopisces) provided some additional examples and context, and is also responsible for compiling and editing the document and (hopefully) keeping it up to date.