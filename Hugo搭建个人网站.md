###Hugo搭建个人网站

config

- _default
  - config.toml
    - site title
    - copyright
    - set content language
  - language.toml
    - [en] file path
    - [zh] file path, 中文菜单标签名称及权重设置
  - menu.toml
    - 英文菜单标签名称及权重设置
  - Params.toml
    - font
    - font_size
    - Description: "Mingming Zhang|张明明，Protocol Security..."
    - email
    - address
    - contact_links (github, skype, telegram...)
    - Sharing_image=figure_name 存在static/media/下的图片文件名
    - Main menu alignment
    - Copyright_license
    - Avatar (shape)
    - Publication (citation style, date format)
    - Projects (views for content)
    - comments

assets

- images
  - icon.png 网页标签logo

static

- media
  - avatar.jpg 头像
- files
  - papers
    - CCS20-SCC.pdf
  - cv
    - zhangmingming_cv.pdf

content

- en
  - authors
    - MingmingZhang
      - avatar.jpg (icon)
      - _index.md （自我介绍）
  - Home (widget名称、排序、数量设置)
    - misc.md （竞赛、荣誉、奖励）
  - publications
    - CCS20-SCC
      - cite.bib
      - index.md 文章介绍、首页publication item标签
      - featured.jpg 文章配图





编译

- 本地预览

  ```
  $ hugo server -D
  
  $ ./view.sh
  ```

- 编译并发布

  ```
  $ hugo -D
  $ git add .
  $ git commit
  $ git push
  ```

  

