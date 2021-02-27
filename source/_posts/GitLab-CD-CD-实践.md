---
title: GitLab CD/CD 实践
date: 2021-10-17 23:06:51
tags: GitLab
---

{% asset_img gitlab.svg "GitLab'GitLab'" %}

# Web 应用的 GitLab CI/CD 配置

## Pipeline

Start -> Install -> Build -> Publish -> Verify -> SonarAndImage -> End

## Install

```yml
Install:
    stage: Install
    image: # docker image link
    variables:
        PROXY: # proxy link
        HTTP_PROXY: $PROXY
        HTTPS_PROXY: $PROXY
    cache:
        key: $CI_COMMIT_REF_SLUG-$CI_COMMIT_SHORT_SHA
        paths:
            - node_modules
    tags:
        - official
    before_script:
        # 项目依赖 git 私有仓库代码，因此将配置好的私钥存储到当前容器中
        - eval $(ssh-agent -s)
        - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add - > /dev/null
        - mkdir -p ~/.ssh
        - chmod 700 ~/.ssh
        - echo "$SSH_KNOWN_HOSTS" > ~/.ssh/known_hosts
        - chmod 644 ~/.ssh.known_hosts
    script:
        - npm install --proxy=$PROXY --https-proxy=$PROXY
    retry: 1
```

## Build

```yml
build:
    stage: Build
    image: # docker image link
    only:
        - release # git branch name
        - /^hotfix/*/
    tags:
        - official
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
    retry: 1
```

## Publish
