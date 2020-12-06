# How to manage the Bohannan Lab website

The Bohannan Lab website, [bohannanlab.org](bohannanlab.org), was created using the `R` package `blogdown` with the [BeautifulHugo](https://themes.gohugo.io/beautifulhugo/) theme from the [Hugo](https://gohugo.io/) framework. For any questions about how to modify anything, seek out the [Hugo documentation](https://gohugo.io/documentation/) or the [`blogdown` book](https://bookdown.org/yihui/blogdown/).

---

## Downloading from and uploading to github

The website source content is at https://github.com/amorris28/bohannan_website and is hosted on [Netlify](netlify.com). Any changes pushed to the github project will automatically be updated through netlify and should be rendered within moments. To modify the website, you need `git` installed. If you want to work on it in R Studio you will also need R and R Studio installed. Alternatively, you can edit the text files with whatever text editor you choose.

On the command line, navigate to the directory on your local computer that you would like to contain the directory for the website source code. Then type:

```
git clone git@github.com:amorris28/bohannan_website.git
```

which will create a new directory called `bohannan_website/` and then download the source code into that directory. 

Each time you sit down to modify the website, use the command line to navigate to
the `bohannan_website/` directory and enter the command:

```
git pull
```

to check that your local files match the github files and to download any changes on github to your local machine so that the two are in
agreement. You can then modify the local files in R Studio or at the command line, whichever you prefer. Once you've made changes, you should navigate back to the  `bohannan_website/` directory and enter:

```
git status
```

which will tell you which files have been modified. If you would like to upload all of the files then type:

```
git add .
git commit -m "your message here"
```

and replace `your message here` with a brief description of what changes you made. Then type:

```
git push
```

which should upload those changes to github and publish them through netlify.

---

## Website Organization

Overall, the website is organized with this directory structure:

```
content/
layouts/
static/
themes/
bohannan_webiste.Rproj
config.toml
index.Rmd
netlify.toml
```

The top directory contains an `.Rproj` in case you're familiar with using R projects in R Studio. It also contains `config.toml` which is the main configuration page for the website where you set up the menus at the top of each page, the website url, etc. `index.Rmd` probably won't change. `netlify.toml` contains the configurations for netlify like what version of Hugo to render the website with. There are also several "hidden" files whose filenames start with a `.`. For example, `.nojekyll` tells netlify not to use `jekyll` to render the website. `.gitignore` is simply a text file that tells git which files not to keep track of. Currently, it ignores an R history or user-specific R settings in `.Rhistory` or `.Rproj.user`. It also ignores the `public/` folder which is where `blogdown` renders the website locally if you use that package to edit and test the website.

---

## Content

The `content/` subdirectory contains the actual webpages as markdown files (`.md`):

```
people/
research/
_index.md
cv.md
join.md
publications.md
research.md
teaching.md
```

`_index.md` contains text that is rendered on the homepage ([bohannanlab.org](bohannanlab.org)) if you'd like to add a blurb there. Each of the other `.md` files is a separate webpage whose url is determined by the name of the file (e.g., `join.md` is the content for [bohannanlab.org/join](bohannanlab.org/join). The `people/` directory contains the content for [bohannanlab.org/people](bohannanlab.org/people) with a separate file for each member of the lab. These files are organized by type of position with a separate directory for PhD students, post-docs, and undergrads. If you would like to add a new PhD student, for example, simply navigate to the `phd/` subdirectory and copy one of the existing files and rename it to the new student's last name and change the content within however you like. Save it within the same folder and push the website to github as described earlier. This should update [bohannanlab.org/people](bohannanlab.org/people) to include that person.

Note: The list of people on [bohannanlab.org/people](bohannanlab.org/people) is organized first by the _date in the individual `.md` file_ and then _alphabetically by the `title` in the individual `.md` files_. Right now, I have it set up so `bohannan.md` has the most recent date to put him first, then the post-docs all have the same date, the phds have the next date, and the undegrads have the next date. That way, they will be organized in order of position (first Brendan, then post-docs, then PhDs, then undergrads) and ordered alphabetically within each of those positions. 

The `research/` subdirectory contains separate pages for each research project with the same organization as the `people/` pages. 

---

## Layout

Back to the top directory, the `themes/` subdirectory contains the default `.html` files that determine how the website looks. The `layout/` subdirectory contains custom `.html` files in the same organization as the `themes/` directory to modify any of the styles on the website. The `layout/` files _override_ the `themes/` files. Any customizations done to the website should be done by copying `themes/` files to the `layout/` directory and editting them there. **These can be safely ignored if you don't want to delve into customizing any html.**

---

## Static

The `static/` directory contains all of the images and files used on the website. `static/files` is currently empty, but is where any `.docx` or `.pdf` files would be saved. `static/img` contains photos, logos, and headshots in, for example, `.jpg` or `.png` format.
