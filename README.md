> **Vue CLI 프로젝트 기반 DevOps 개발환경 실습**


![3-0](https://user-images.githubusercontent.com/69946102/125096647-519e7e80-e110-11eb-8138-1d5f7097c1c8.png)
1) PC에 'yarn'을 설치한 이후, `vue create vue-devops` 명령어 실행 후, 위와 같은 설정으로 Vue 프로젝트를 생성한다.

![3-1](https://user-images.githubusercontent.com/69946102/125096771-69760280-e110-11eb-8b22-bef0006cfd46.png)

2) 원하는 경로에 'vue-devops' 프로젝트가 생성되었다.

![3-2](https://user-images.githubusercontent.com/69946102/125096783-6d098980-e110-11eb-9d81-9ce893fa11cd.png)

3) 'vue-devops'경로로 이동해, `yarn serve`를 통해 로컬서버를 실행한다.

![3-3](https://user-images.githubusercontent.com/69946102/125096796-6ed34d00-e110-11eb-81f3-a1f0b92d3c28.png)

4) 로컬서버에서 위를 확인할 수 있다. 'Ctrl+C'로 터미널에서 서버를 종료한다.

![3-4](https://user-images.githubusercontent.com/69946102/125096815-74309780-e110-11eb-9401-102f92c50472.png)
![3-5](https://user-images.githubusercontent.com/69946102/125096830-772b8800-e110-11eb-9dad-1e8e1553ce2c.png)

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

![3-6](https://user-images.githubusercontent.com/69946102/125096879-83afe080-e110-11eb-9302-f71700e5144a.png)

8) `yarn deploy`명령을 통해, 빌드된 파일을 원격 저장소의 gh-pages 브랜치를 생성해 푸시된다.

![3-7](https://user-images.githubusercontent.com/69946102/125096895-86aad100-e110-11eb-89b0-38641d5e2001.png)

9) GitHub 레포의 'Settings > Pages' 탭에 들어가, 배포된 주소에 접속해 빌드된 파일을 확인할 수 있다. 

</br>

---

이전의 과정을 통해, 코드의 커밋&푸쉬만으로 서버에서 빌드와 배포를 자동화로 할 수 있는 작업을 마쳤다. 이후 배포한 코드의 테스트를 할 수 있는 단계를 마련하였다.

![3-8](https://user-images.githubusercontent.com/69946102/125096906-8a3e5800-e110-11eb-8758-c1db82544af8.png)

1) Simple workflow 파일(deploy.yml)을 작성한다. 레포의 'Actions'탭에서 'Set up this workflow'에 들어가 기본 코드를 위와 같이 설정한다. ~~아래 코드는 생략~~

![3-9](https://user-images.githubusercontent.com/69946102/125096917-8d394880-e110-11eb-935b-4656a0fcee39.png)

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

![3-10](https://user-images.githubusercontent.com/69946102/125096941-932f2980-e110-11eb-8ad7-27a83a42ac48.png)

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

![3-11](https://user-images.githubusercontent.com/69946102/125096956-975b4700-e110-11eb-8ab4-37109cd25f8d.png)

5) 실패 test를 하기 위해, 'deploy.yml'을 위와 같이 수정한 후, 'HelloWord.vue'의 h1 탭의 내용을 수정한다. (이 경우, 전달받은 msg를 사용하지 않아 오류가 발생하게 된다.) (커밋&푸쉬)

</br>

6) 다시 성공 케이스를 만들기 위해, 'HelloWorld.vue'의 내용을 원래대로 되돌리고, 'App.vue'에서 msg로 '테스트 성공'을 전달한다.

</br>

![3-20](https://user-images.githubusercontent.com/69946102/125096982-9d512800-e110-11eb-8579-c48672721067.png)

![3-21](https://user-images.githubusercontent.com/69946102/125096993-a04c1880-e110-11eb-8e4b-603af4017285.png)

**완료.** (두번째 성공 액션이 실패로 기록되어 있는 것은 .yml 파일에 오타가 난 것을 마지막에 가서야 확인했기 때문이다.😂)
