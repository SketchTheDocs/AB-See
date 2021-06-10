# Under The Hood

 * Website build with the [Hugo](https://gohugo.io) static site generator.
 * Using the [hugo-swift-theme](https://github.com/onweru/hugo-swift-theme) theme.
 * Deployed to the [GitHub Pages](http://sketchthedocs.dev/AB-See) for this repo.

I do the basic setup based on the default [Quickstart](https://gohugo.io/getting-started/quick-start/) guide. Here are the steps I followed for convenience.

---

`STEP 1`:  Install hugo for development.

```
$ brew install hugo
$ hugo version
hugo v0.83.1+extended darwin/amd64 BuildDate=unknown
```
---

`STEP 2`: Create new hugo project (source directory)

```
$ hugo new site www
```

---

`STEP 3`: Add A Theme

Follow [theme directions](https://gohugo.io/getting-started/quick-start/#step-3-add-a-theme) - you should be able to substitute [a different theme](https://themes.gohugo.io/) and use the same process.

```
$ cd www
$ git submodule add it submodule add https://github.com/onweru/hugo-swift-theme.git themes/hugo-swift-theme

```
---

`STEP 4`: Customize The Theme

First, edit `www/config.toml` to specify theme.

 Next use `publishDir` to specify a different target folder for build (default is _public_) if needed. I use _docs/_ given it's a default endpoint for GitHub Pages.


```
# Standard attributes 
baseURL = "https://sketchthedocs.dev/AB-See/"
languageCode = "en-us"
title = "AB-See by @SketchTheDocs"
theme = "hugo-swift-theme"

# Specify (new) Build directory
publishdir = "../docs" 
```

Finally, follow guidance from the [theme's website](https://github.com/onweru/hugo-swift-theme) for additional customization.

---

`STEP 5`: Test your website locally

Keep it running to see changes as you make them (hot reload). Use "-D" if you want to also see posts that are still in _draft_ status.

```
$ cd www/
$ hugo server -D
..
..
Web Server is available at http://localhost:1313/AB-See/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

---
`STEP 6`: BUILD FOR DEPLOYMENT

The default build directory is local.
Followed this [guidance](https://gohugo.io/getting-started/quick-start/) I updated my `www/config.toml` to specify a custom _publishdir_ ("docs") as the target.

Now just build the static website from this source using this command:

```
$ cd www/
$ hugo -D
```
 
`Note`: Go into the GitHub Settings for gh-pages and point it to the "docs/" folder. You static website is now automatically hosted at the GitHub Pages endpoint for this repo.



## Troubleshooting

 - Fixed DNS issue (baseUrl update)
 - Fixed side-menu absence (added designer.yml, menu.yml files in data/ folder)
 - Fixed Archives link error (two steps: create new posts in the `content/post` folder, add a `content/_index.md` placeholder)
 - Fixed integrity issue (read below)


INTEGRITY ISSUE:
 * Using default theme results in an error as follows: "xxx has an integrity attribute, but the resource requires the request to be CORS enabled to check the integrity, and it is not. The resource has been blocked because the integrity cannot be enforced."
 * See [this Stackoverflow message](https://stackoverflow.com/questions/35323268/how-to-solve-resource-requires-the-request-to-be-cors-enabled-resource-has-bee) to understand the cause - a CORS issue which requires `crossorigin="anonymous"` to be set alongside the integrity attribute.
 * The fix should be in the theme itself (currently integrity checked in two partial templates - head and footer - one of which has the attribute and the other not)
 * Will submit PR - in the meantime, created my copy of partial to override theme and fixed it there.


## Maintenance

Simply add content into the relevant subfolders using something of the form below, where `xxxxx` is a slug that is unique to the site.

```
$ hugo new posts/xxxxx.md
```

This generates a new file under the `content/posts` directory. If your local server was running, the changes should show up instantly in the browser (hot reload).

This theme assumes `static/images` to be the root for loading images. Anything placed in this directory can be referenced by name in METADATA (e.g., `image: test.png`) or by relative URL in the BODY (e.g., `/images/test.png`) of content posts.

To build/deploy, the current process is manual. Simply run the command below to get the `docs/` target directory updated. Then commit the changes to GitHub, which will auto-refresh the static GH Pages deployment endpoint.

```
$ cd www/
$ hugo -D
```

`TODO:` Automate this step with GitHub Actions.