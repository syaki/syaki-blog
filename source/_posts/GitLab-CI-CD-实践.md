---
title: GitLab CI/CD 实践
date: 2020-10-21 23:06:51
tags: [GitLab, Java, JavaScript]
---

{% asset_img gitlab.svg "GitLab'GitLab'" %}

# JavaScript Web 应用的 GitLab CI/CD 配置

## Pipeline

Start -> Install -> Build -> Publish -> Verify -> End

## Install

通过 npm install 安装依赖包，并将结果缓存到 cache 里。

```yml
script:
    - npm install --proxy=$PROXY --https-proxy=$PROXY
cache:
    key: $CI_COMMIT_REF_SLUG-$CI_COMMIT_SHORT_SHA
    paths:
        - node_modules
```

如果项目依赖私有 git 仓库和代理。

SSH_PRIVATE_KEY 和 SSH_KNOWN_HOSTS 为配置好的 GitLab CI/CD 变量。

```yml
variables:
    PROXY: # proxy link
    HTTP_PROXY: $PROXY
    HTTPS_PROXY: $PROXY
before_script:
    # 项目依赖 git 私有仓库代码，因此将配置好的私钥存储到当前容器中
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add - > /dev/null
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - echo "$SSH_KNOWN_HOSTS" > ~/.ssh/known_hosts
    - chmod 644 ~/.ssh.SSH_KNOWN_HOSTS
```

## Build

从缓存中拉取 Install 的结果，运行自定义构建命令并将生成的结果存入 artifacts 。

```yml
variables:
    <<: *node-variables
    <<: *organization-variables
cache:
    key: $CI_COMMIT_REF_SLUG-$CI_COMMIT_SHORT_SHA
    paths:
        - node_modules
    # 拉取 Install 阶段生成的 node_modules
    policy: pull
artifacts:
    # 将自定义构建后的资源存储到 artifacts 中
    paths:
        - dist/ # 自定义构建后的资源路径
script:
    - npm run build # 自定义构建命令
```

## Publish

使用构建好的镜像，发布静态资源。

```yml
image: # 发布静态资源的镜像地址
script: publish # 发布命令
```

## Verify

一些自动化测试，代码静态分析和代码格式化。

# Java Web 应用的 GitLab CI/CD 配置

## Pipeline

Start -> Complie -> Package -> Image -> Deploy -> End

## Complie

利用 maven 对代码进行 `clean compile` ，将 verify-package.yml 报告存入 artifacts ，并将生成的 target 存入 cache 。

```yml
caches:
    - key: compile-cache
      paths:
          - $POM_DIR/**/target/classes
          - $POM_DIR/**/target/generated-sources
          - $POM_DIR/**/target/maven-status
      policy: push
artifacts:
    reports:
        snapshot: '$POM_DIR/**/target/verify-package.yml'
```

## Package

Package 依赖 Compile 。

从 cache 中拉取 compile cache 。并将打包结果存入 cache 。

```yml
needs:
    - Compile
caches:
    - key: compile-cache
      paths:
          - $POM_DIR/**/target/classes
          - $POM_DIR/**/target/generated-sources
          - $POM_DIR/**/target/maven-status
      policy: pull
    - key: ${CACHE_KEY}-target
      paths:
          - ./**/*.war
          - ./**/*.zip
      policy: push
script:
    - mvn package
```

## Image

利用 package 的 cache 和构建好的基础环境镜像构建应用镜像放入 cache 中。

```yml
caches:
    - key: ${CACHE_KEY}-target
      paths:
          - ./**/*.war
      policy: pull
    - key: ${CACHE_KEY}-image-repo-file
      paths:
        - ${IMAGE_REPO_FILE}
     policy: push
script:
    - docker login
    - image build
```

## Deploy

拉取 cache 中构建好的应用镜像，发布到机器上。
