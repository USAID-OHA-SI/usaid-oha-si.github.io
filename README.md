## Welcome to Global Health / OHA Github Site

You can access the live site at [USAID-OHA-SI](https://usaid-oha-si.github.io/).

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Maintenance

This repo is used to maintain USAID-OHA-SI's website. All pages should be updated and publish through this repo.

### Setting up Jekyll on your local host

GitHub uses Jekyll, a Ruby Gem, to update the website on _github.io_

For you to preview and test the site locally before pushing your updated code, you will need to install Jekyll.

Follow these [instructions](https://jekyllrb.com/docs/installation/) to install Jekyll and all the related requirements.

### Publishing a tutorial or blog post

All new publications (tutorials or blog) will be processed as a blog posts and you will need to meet few requirements:

1. File type

Markdown files (.md) are recommended 

2. File name

You post will need to names as follow: YYYY-MM-DD-Sample-Post-Title-Here.md
Dashes are used as space markers, so "Sample-Post-Title-Here" will end up as "Sample Post Title Here"
Here is an example:
`2020-04-20-Processing-HFR-Country-Data-with-Wavelenth-R.md`

3. File content

    3.1. File Header

    Jekyll uses **YAML Front Matter** as variable holders for your post.
    The header should be always wraped within a pair of 3 dashes and contain a _layout_, _title_, _date_ (in YYYY-MM-DD format), and _author_.
    One could also include _categories_ in the header to group posts by category. You may use more that one categories. Tags are also allowed.
    ```
    ---
    layout: post
    title: "Sample Post Title Here"
    date: 2020-04-20
    author: "Baboyma Kagniniwa"
    categories: [Tutorial, HFR, Reporting]
    tags: [R, MySQL, Frequency]
    ---
    ```

    3.2. Post Description

    Jekyll uses the first paragraph as the excerpt of your post. Please do make sure that your first paragraph represent a short description of your post.

    3.3. Post Content

    The rest of the content is basically the content of your post. Make sure to use valid markdown synthax. This content will be translated into an html page.

4. File location

All tutorials and/or blog posts should go under **_posts** folder. For tutorials specific posts, make sure to add "tutorial" in the categories list in the header of your post.  


### Updating the site with the new content

You should alway test the new content before publishing the site. 
Follow the setup section to install Jekyll. Once done with your post, run `jekyll serve`, open a browser, and go to `localhost:4000` to review the changes.


### Markdown Syntax

Quick Markdown references are below:

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/USAID-OHA-SI/usaid-oha-si.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
