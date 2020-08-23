# oss-website-demo-spa-vue

[Demo](http://oss-spa-demo.fangbinwei.cn/)

[ci config](https://github.com/fangbinwei/oss-website-demo-spa-vue/blob/master/.github/workflows/test.yml)

```yml
name: deploy test

on:
  push:
    branches:
      - master

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
      # load repo to /github/workspace
    - uses: actions/checkout@v2
    - name: Use Node.js 12.x
      uses: actions/setup-node@v1
      with:
        node-version: 12.x
    - run: npm install yarn@1.22.4 -g

    - name: Get yarn cache directory path
      id: yarn-cache-dir-path
      run: echo "::set-output name=dir::$(yarn cache dir)"

    - uses: actions/cache@v2
      id: yarn-cache # use this to check for `cache-hit` (`steps.yarn-cache.outputs.cache-hit != 'true'`)
      with:
        path: ${{ steps.yarn-cache-dir-path.outputs.dir }}
        key: ${{ runner.os }}-yarn-${{ hashFiles('**/yarn.lock') }}
        restore-keys: |
          ${{ runner.os }}-yarn-
    - run: yarn install
    - run: yarn build
    - name: upload files to OSS
      uses: fangbinwei/aliyun-oss-website-action@v1
      with:
          accessKeyId: ${{ secrets.ACCESS_KEY_ID }}
          accessKeySecret: ${{ secrets.ACCESS_KEY_SECRET }}
          bucket: website-spa-vue-demo
          endpoint: oss-spa-demo.fangbinwei.cn
          cname: true
          folder: dist
          notFoundPage: index.html
          htmlCacheControl: no-cache
          imageCacheControl: max-age=864001
          otherCacheControl: max-age=2592001
```