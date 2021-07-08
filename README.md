> **Vue CLI 프로젝트 기반 DevOps 개발환경 실습**

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/26ec1fc8-aff6-4b26-8cb3-2d7eb892b830/3-0.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/26ec1fc8-aff6-4b26-8cb3-2d7eb892b830/3-0.png)

1) PC에 'yarn'을 설치한 이후, `vue create vue-devops` 명령어 실행 후, 위와 같은 설정으로 Vue 프로젝트를 생성한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2824bf00-4eda-48f1-ac00-aa326e530742/3-1.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2824bf00-4eda-48f1-ac00-aa326e530742/3-1.png)

2) 원하는 경로에 'vue-devops' 프로젝트가 생성되었다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4f23494e-2e0f-4406-bd90-8391d669091e/3-2.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4f23494e-2e0f-4406-bd90-8391d669091e/3-2.png)

3) 'vue-devops'경로로 이동해, `yarn serve`를 통해 로컬서버를 실행한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/afcd9109-aec2-4753-a621-d73564882ea9/3-3.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/afcd9109-aec2-4753-a621-d73564882ea9/3-3.png)

4) 로컬서버에서 위를 확인할 수 있다. 'Ctrl+C'로 터미널에서 서버를 종료한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f55898ca-848f-442a-a373-d2c04939057e/3-4.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f55898ca-848f-442a-a373-d2c04939057e/3-4.png)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/965c22f2-39da-406d-ba18-d26b78c6221c/3-5.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/965c22f2-39da-406d-ba18-d26b78c6221c/3-5.png)

5) 이후, 위와 같은 명령어로 git과 연동이 가능했고, 결과를 확인할 수 있었다. 실제로, GitHub에서는 설정해둔 레포에 여러 파일들이 생성된 것을 확인할 수 있다.

```powershell
C:\vue-devops>yarn add gh-pages -D

# package.json 에서 필요한 정보 추가 및 수정
"homepage" : "https://xoxaeux.github.io/vue-devops",

"scripts": {
  "serve": "vue-cli-service serve",
  "build": "vue-cli-service build",
  "predeploy" : "vue-cli-service build",
  "deploy": "gh-pages -d dist",
  "clean":"gh-pages-clean",
  "test:unit": "vue-cli-service test:unit",
  "lint": "vue-cli-service lint"
},
```

6) GitHub Pages로 배포하기 위한 라이브러리를 추가하고, package.json에서 배포에 필요한 정보를 추가한다.

```powershell
# vue.config.js 생성 후 내용 추가
module.exports = {
    publicPath: "/vue-devops/",
    outputDir: "dist"
}
```

7) 위의 설정으로, 페이지 확인이 가능하게 된다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/77334218-7e69-4f21-bad5-8f286eed9679/3-6.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/77334218-7e69-4f21-bad5-8f286eed9679/3-6.png)

8) `yarn deploy`명령을 통해, 빌드된 파일을 원격 저장소의 gh-pages 브랜치를 생성해 푸시된다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/77154bdd-40e4-4c16-8153-f12de68277c3/3-7.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/77154bdd-40e4-4c16-8153-f12de68277c3/3-7.png)

9) GitHub 레포의 'Settings > Pages' 탭에 들어가, 배포된 주소에 접속해 빌드된 파일을 확인할 수 있다. 

</br>

---

이전의 과정을 통해, 코드의 커밋&푸쉬만으로 서버에서 빌드와 배포를 자동화로 할 수 있는 작업을 마쳤다. 이후 배포한 코드의 테스트를 할 수 있는 단계를 마련하였다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b38d2bf0-bda5-4351-8436-0d6783ae1044/3-8.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b38d2bf0-bda5-4351-8436-0d6783ae1044/3-8.png)

1) Simple workflow 파일(deploy.yml)을 작성한다. 레포의 'Actions'탭에서 'Set up this workflow'에 들어가 기본 코드를 위와 같이 설정한다. ~~아래 코드는 생략~~

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7b0bfb9e-2aac-4e58-b004-5bcd4d28851b/3-9.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7b0bfb9e-2aac-4e58-b004-5bcd4d28851b/3-9.png)

2) 위와 같이 커밋과 동시에 workflow가 동작함을 확인할 수 있다.

```yaml
deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout source code
        uses: actions/checkout@master
      - name: Set up Node.js
        uses: actions/setup-node@master
        with:
          node-version: 14.x
      - name: Install dependencies
        run: yarn install
      - name: Build page
        run: yarn build
        env:
          NODE_ENV: production
      - name: Deploy to gh-pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

3) IDE에서 '.github/workflows/deploy.yml'에 위와 같이 내용을 추가한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/928b64c9-851b-4b68-8e48-09a25b27fa6c/3-10.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/928b64c9-851b-4b68-8e48-09a25b27fa6c/3-10.png)

4) 수정한 후, 'SOURCE CONTROL'탭에서 커밋하고, VS Code의 최하단에서 commit된 내용을 푸시할 수 있다.

```yaml
deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout source code
        uses: actions/checkout@master
      - name: Set up Node.js
        uses: actions/setup-node@master
        with:
          node-version: 14.x
      - name: Install dependencies
        run: yarn install
      - name: Test unit
        run: yarn test:unit
      - name: Build page
        run: yarn build
        env:
          NODE_ENV: production
      - name: Deploy to gh-pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd0392aa-0bef-4eff-8a21-a918048c7ac5/3-11.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd0392aa-0bef-4eff-8a21-a918048c7ac5/3-11.png)

5) 실패 test를 하기 위해, 'deploy.yml'을 위와 같이 수정한 후, 'HelloWord.vue'의 h1 탭의 내용을 수정한다. (이 경우, 전달받은 msg를 사용하지 않아 오류가 발생하게 된다.) (커밋&푸쉬)

</br>

6) 다시 성공 케이스를 만들기 위해, 'HelloWorld.vue'의 내용을 원래대로 되돌리고, 'App.vue'에서 msg로 '테스트 성공'을 전달한다.

</br>

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0e997da3-6ac0-4fe2-8a7e-f31c45c56f6c/3-21.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0e997da3-6ac0-4fe2-8a7e-f31c45c56f6c/3-21.png)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7559d502-3008-41de-9637-469130f5ee3c/3-20.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7559d502-3008-41de-9637-469130f5ee3c/3-20.png)

**완료.** (두번째 성공 액션이 실패로 기록되어 있는 것은 .yml 파일에 오타가 난 것을 마지막에 가서야 확인했기 때문이다.😂)
