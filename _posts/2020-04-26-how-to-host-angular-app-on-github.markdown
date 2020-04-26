---
layout: post
title:  "How to Host Angular App on GitHub"
categories: "angular github"
---

I have written an Angular app to track NSW COVID-19 cases [Visit this app in a new tab](https://yuezhizizhang.github.io/nsw-covid-19){:target="_blank"}. You can see this app is hosted on GitHub. Actually, it is very easy to host your Angular app on GitHub. Take my project [nsw-covid-19](https://github.com/yuezhizizhang/nsw-covid-19){:target="_blank"} for instance:

### 1. Install angular-cli-ghpages globally

```cmd
npm install -g angular-cli-ghpages
```

### 2. Production build

> package.json
```
"build:prod": "ng build --prod --base-href /nsw-covid-19/"
```

Build the app for production. The --base-href is pointing to the "/&lt;repository-name&gt;/" on GitHub. On GitHub, my repository is named "nsw-covid-19".

### 3. Make sure the files are generated right under the dist folder

After running the build command, I found the files are generated under "dist/nsw-covid-data". It's because when I initiated the Angular app, I called it "nsw-covid-data". In **angular.json** file, the "defaultProject" is "nsw-covid-data". And in the build option, the default output path is pointing to:

> angular.json
```json
"build": {
  "builder": "@angular-devkit/build-angular:browser",
  "options": {
    "outputPath": "dist/nsw-covid-data",
    ...
  }
}
```

You can either change the outputPath to dist, or you can run a shell script to move the files up from "dist/nsw-covid-data" to "dist".

> ngh.sh
```shell
mv dist/nsw-covid-data/* dist/ && rm -rf dist/nsw-covid-data
```

### 4. ngh

```cmd
ngh
```

Now, you are able to visit your app hosted at https://username.github.io/repository-name. In my example, it's at https://yuezhizizhang.github.io/nsw-covid-19.