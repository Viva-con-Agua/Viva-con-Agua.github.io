# Writing Documentation

The documentation you are currently viewing is based on [MkDocs](https://www.mkdocs.org/) which is a simple markdown based documentation system.
We use it because of its low maintenance burden, high popularity and ease of deployment via GitHub pages.

If you are looking for upstream documentation, look here:

- [MkDocs *Writing your docs*](https://www.mkdocs.org/user-guide/writing-your-docs/)
- [MkDocs general *User Guide*](https://www.mkdocs.org/user-guide/)
- [Material for MkDocs User Guide](https://squidfunk.github.io/mkdocs-material/)
- [Material for MkDocs Reference](https://squidfunk.github.io/mkdocs-material/reference/)

## Adding a new page

Adding a new page is very easy since you only need to create a markdown file inside the `docs` folder (see project layout below) and then create a link to it for navigation (inside `mkdocs.yml`).

It generally does not matter where you place your file but we prefer to keep the files in the same structure as the navigation sections.

- **Tutorials** are used to teach a reader a new subject or lesson.
  It is generally written in easy language and allows the newcomer to get started.
  See [here](https://documentation.divio.com/tutorials/) on how to write good tutorials.
- **How-to guides** serve to show how a specific goal can be accomplished like this page shows you how to create a new doc page.
  See [here](https://documentation.divio.com/how-to-guides/) on how to write good guides.
- **References** are informative in nature and serve describe a specific solution in detail.
  Often, reference documentation can be found not here but as inline docstrings directly in software packages.
  See [here](https://documentation.divio.com/reference/) on how to write good references.
- **Explanations** explain *why* something is done the way that it is done.
  See [here](https://documentation.divio.com/explanation/) on how to write good explanations. 

Generally refer to the [Divio documentation system](https://documentation.divio.com/introduction/) on how to write good documentation.

## Deployment

The built documentation is automatically deployed on GitHub pages at [viva-con-agua.github.io](https://viva-con-agua.github.io/).
There is nothing you need to do to re-deploy it except push your changes to GitHub.

## Project layout

    mkdocs.yml                          # The MkDocs configuration file
    docs/
        home/                           # A section folder for the "Home" section
            index.md                    # The documentation homepage
            documentation_system.md     # The page you are currently vewing
            ...                         # Other markdown pages, images and other files


