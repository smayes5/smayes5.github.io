# Steve Mayes' Personal Web Site

This repository contains the code base used to build [https://smayes.com](https://smayes.com).

## Design
The original design was sourced from [HTML5 UP](https://html5up.net)'s [Story template](https://html5up.net/story); to which I've made some minor modifications. The stock imagery on the site is sourced from [Unsplash](https://unsplash.com).

## Technologies
The web site is a static web site built with [Jekyll](https://jekyllrb.com) and [Markdown](https://daringfireball.net/projects/markdown). [Visual Studio Code](https://code.visualstudio.com) is my preferred IDE.

## Continuous Deployment Process
I'm leveraging GitHub Pages for my Continuous Deployment process and for hosting.

## Testing Locally
I'm currently using Ubuntu on Windows 10 to test my site locally before merging any pull requests.

```bash
cd /mnt/c/Code/smayes5.github.io/
bundle exec jekyll serve --no-watch
```
