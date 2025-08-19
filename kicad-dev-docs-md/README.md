This the source for the kicad developer documentation

# Prerequisites

You will need these packages:

- [hugo](http://gohugo.io/) version 0.110.0 or higher

- [ruby](https://www.ruby-lang.org) (to use asciidoctor)

- [asciidoctor](http://asciidoctor.org/) version 2.0.10

Using asciidoctor is a requirement, because the original asciidoc runs
into trouble parsing the adoc files with TOML headers in them.
asciidoctor also has a few extra features for web pages.

# Testing

Execute the hugo command in the repository root to build and serve the
files for development:

    hugo serve

Observe the console output as it will tell you the address where the
page is accessible in a browser. The -w flag tells it to watch the
filesystem for changes to rebuild automatically. Also, the page in the
browser will autorefresh once the rebuild completes successfully.

# I18n

Edit config.toml file. add \[Languages.zh_cn\]
\[\[Languages.zh_cn.menu.shortcuts \]\] Content.

    [Languages.zh-cn]
    title = "开发者文档 | KiCad"
    weight = 1
    languageName = "简体中文"
    landingPageURL = "/zh-cn"
    landingPageName = "<i class='fas fa-home'></i> 主页"

    [[Languages.zh-cn.menu.shortcuts]]
    name = "<i class='fab fa-fw fa-gitlab'></i> GitLab 仓库"
    identifier = "ds"
    url = "https://gitlab.com/kicad/services/kicad-dev-docs"
    weight = 10
