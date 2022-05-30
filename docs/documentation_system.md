# Documentation System

The documentation you are currently viewing is base on [docsify](https://docsify.js.org/#/) which is a simple
and light-weight documentation system that renders markdown files from a webserver.
We use it because of its low maintenance burden and ease of deployment via GitHub pages.

This page aims to describe the file and folder structure of this system so that you can easily add new content.

## Testing locally

There is not much that can be previewed since *docsify* renders the markdown file fairly straight-forward so if the file you are editing looks right in your IDE, it will probably look right in the end too.

Should you wish to test the system anyway, perform the following steps in a terminal from the repository root:
```shell
npm install
npm run serve
```

## Adding a new page

Adding a new page super easy since it only requires two steps:
1. Add the markdown file where it makes sense the *docs* folder.

   To *docsify* it really doesn't matter where a specific file is put however to make maintenance of our
   documentation easier, pages should be organized.
   E.g. a page describing a feature of the *vcago* package should be put inside the *packages/vcago* folder while
   a small page describing a single thing about our organization (like this page) can be put directly into *docs*.

2. Link to the page from *_sidebar.md*

   The page you added would be perfectly valid and renderable by *docsify* but the user won't magically know the correct URL to it.
   Adding a link to the newly added page to *_sidebar.md* renders a link to the new page inside the left sidebar so that it is discoverable by a user.
