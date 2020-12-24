---
title: 使用 Github Actions 自动构建部署 Hexo 博客
name: Use-Github-Actions-to-automatically-deploy-Hexo-Blog
keywords: Github Actions, Hexo 自动部署, Hexo CI/CD
image: https://s3.ax1x.com/2020/12/24/rc4UoQ.png

date: 2020-11-02 21:36:50
categories:
  - CI/CD
tags: 
  - CI/CD
  - GitHub Actions
---

## 介绍

这篇文章记录一下使用 GitHub Actions 一步一步实现 [Hexo Blog](https://hexo.io/) 自动化构建部署到 GitHub Pages 的流程。


[GitHub Actions](https://docs.github.com/cn/free-pro-team@latest/actions) 是类似于 [Travis CI](https://travis-ci.org/) 、 [Circle CI](https://circleci.com/) 这样的持续集成服务， 是由 GitHub 官方推出的持续集成服务，可以帮助我们完成一些自动化测试、打包、部署等操作。


GitHub Actions 中的常见术语:

- `workflow` 表示一次持续集成的过程
- `job` 表示构建任务，一个 workflow 可以由一个或者多个 job 组成，可支持并发执行多个 job
- `step` 一个 job 由一个或多个 step 组成，按顺序依次执行
- `action` 每个 step 由一个或多个 action 组成，按顺序依次执行

<br />

**官方文档：**

- [https://help.github.com/en/actions](https://help.github.com/en/actions) (English)
- [https://help.github.com/cn/actions](https://help.github.com/cn/actions) (中文)


## 准备

创建一个 `username.github.io` 的仓库， `username` 为自己 GitHub 的用户名，在这个仓库中采用的一个仓库多分支的方式，一个分支存放博客源码，主分支用来存放生成的网站静态资源。

### GitHub 秘钥配置

使用命令生成秘钥：该命令会在当前目录下生成两个文件， 分别为公钥 `github-deploy-key.pub` 和私钥 `github-deploy-key`

``` bash
ssh-keygen -f github-deploy-key
```

将创建好的 `github-deploy-key.pub` 文件中的内容复制添加到对应仓库的 Deploy keys 中， **Settings** / **Deploy keys** / **Add deploy key**， 取名为 `HEXO_DEPLOY_KEY_PUB`。

![HEXO_DEPLOY_KEY_PUB](https://s3.ax1x.com/2020/12/24/rc4kxx.png)

将创建好的 `github-deploy-key` 文件中的内容复制添加到对应仓库的 Secrets 中， **Settings** / **Secrets** / **New Secret**，取名为 `HEXO_DEPLOY_KEY_PRI`。

![HEXO_DEPLOY_KEY_PRI](https://s3.ax1x.com/2020/12/24/rc4Mid.png)


需要注意的是，这里取的名字在 Actions 脚本中通过 `${{secrets.HEXO_DEPLOY_KEY_PRI}}` 获取时需要一致。


## Github Actions 脚本编写 

在仓库的 `.github/workflows/` 目录下创建一个 `hexo-auto-deploy-ci.yml` 文件，这个文件名字可以是随意取得。

我的 Actions 脚本配置如下：

``` yml
name: Hexo Auto Deploy CI

on:
  push:
    branches: [source]
  pull_request:
    branches: [source]

env:
  GIT_USER: iaosee
  GIT_EMAIL: iaosee@outlook.com
  THEME_REPO: iaosee/hexo-theme-zhaoo
  THEME_BRANCH: master

jobs:
  build:
    name: Build on node ${{ matrix.node_version }} and ${{ matrix.os }}
    runs-on: ubuntu-latest
    strategy:
      matrix:
        os: [ubuntu-latest]
        node_version: [12.x]

    steps:
      - name: Checkout blog repo
        uses: actions/checkout@v2
        with:
          ref: source

      - name: Checkout theme repo
        uses: actions/checkout@v2
        with:
          repository: ${{ env.THEME_REPO }}
          ref: ${{ env.THEME_BRANCH }}
          path: themes/zhaoo

      - name: Use Node.js ${{ matrix.node_version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node_version }}

      - name: Configuration environment
        env:
          HEXO_DEPLOY_KEY_PRI: ${{secrets.HEXO_DEPLOY_KEY_PRI}}
        run: |
          sudo timedatectl set-timezone "Asia/Shanghai"
          mkdir -p ~/.ssh/
          echo "$HEXO_DEPLOY_KEY_PRI" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name $GIT_USER
          git config --global user.email $GIT_EMAIL
          cp _config.theme.zhaoo.yml themes/zhaoo/_config.yml

      - name: Install dependencies
        run: |
          npm i

      - name: Deploy blog
        run: |
          npm run clean
          npm run deploy
```


### 运行脚本


现在，只要仓库的 `source` 分支有代码推送，GitHub 就会创建一个容器来运行这里配置的任务，解放双手，Enjoy It 😃。

![Actions 脚本运行结果](https://s3.ax1x.com/2020/12/24/rc4UoQ.png)

