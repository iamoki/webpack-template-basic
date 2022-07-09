# Webpack Setting Process
>Webpack 기본세팅

## Step1.
**npm 프로젝트 시작(package.json생성)**
~~~bash
npm init -y
~~~

webpack, webpack-cli, webpack-dev-server 패키지 설치  
(웹팩 데브서버는 웹팩 cli와 메이저버전을 일치시켜줘야하기때문에 @next 키워드 추가)
~~~bash
npm i -D webpack webpack-cli webpack-dev-server@next
~~~

package.json - 개발서버 오픈 명령어, 빌드 명령어 수정
~~~json
"scripts": {
    "dev": "webpack-dev-server --mode development",
    "build": "webpack --mode production"
}
~~~
---
## Step2.
**webpack.config.js 파일 생성후 기본적인 구성옵션추가**
* [entry](https://webpack.kr/concepts/#entry)
* [output](https://webpack.kr/concepts/#output)
~~~javascript
const path = require('path');

module.exports = {

    // 파일을 읽어들이기 시작하는 진입점 설정(웹팩의 기본 진입점은 js파일)
    entry: './js/main.js',
    // 결과물(번들)을 변환하는 설정
    output: {
        // 어떤 경로에 결과물을 내어줄껀지(node.js에서 필요로 하는 절대경로가 필요)
        path: path.resolve(__dirname,'dist'),
        // 그 결과는 entry와 동일하게 지정해줄수 있음
        filename: 'main.js',
        // 설정시 기존에 만들어진 파일을 제거할 수 있음
        clean: true
    }
}
~~~

html-webpack-plugin 패키지 설치
~~~bash
npm i -D html-webpack-plugin
~~~
webpack.config.js 파일 수정
~~~javascript
const HtmlPlugin = require('html-webpack-plugin');

module.exports = {
    ...,
    // 번들링 후 결과물의 처리 방식 등 다양한 플러그인 설정
    plugins: [
        new HtmlPlugin({
            // 루트경로에 만들어둔 index.html파일
            template: './index.html'
        }),
    ],
    // 포트번호 주소가 잘못나올 시 설정
    devServer: {
        host: 'localhost'
    }
}
~~~
---
## Step3.
**favicon, img 파일 연결하기**  
static 폴더 생성 후 favicon 파일 삽입, 그리고 하위 images폴더에 png파일 삽입

copy-webpack-plugin 설치
~~~bash
npm i -D copy-webpack-plugin
~~~
webpack.config.js 내용 추가
~~~javascript
const CopyPlugin = require('copy-webpack-plugin');

module.exports = {
    ...,
    plugins: [
        ...,
        new CopyPlugin({
            patterns: [
                // static 폴더에 있는 내용이 복사가 되어 dist 폴더에 삽입됨
                { from: 'static' }
            ]
        })
    ],
    ...
}
~~~
---
## Step4.
**CSS 설정**  
main.js 파일에 css 파일 import시키고
~~~javascript
import '../css/main.css'
~~~
CSS를 읽어올수 있도록 외부 패키지 설치후 webpack.config.js 수정
~~~bash
npm i -D css-loader style-loader
~~~
~~~javascript
module.exports = {
    ...,
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    // html파일에 해석된 css 스타일을 삽입
                    'style-loader',
                    // js파일에 있는 css를 해석
                    'css-loader',
                ]
            },
        ]
    },
    ...
}
~~~
---
## Step5.
**설정한 CSS를 SCSS로 수정하기**  
main.js에 있는 css 를 scss로 수정후 추가 loader 설치
~~~javascript
import '../scss/main.scss'
~~~

~~~bash
npm i -D sass-loader sass
~~~

webpack.config.js 수정
~~~javascript
module.exports = {
    ...,
    module: {
        rules: [
            {
                test: /\.s?css$/,
                use: [
                    ...,
                    'sass-loader'
                ]
            },
        ]
    },
    ...
}
~~~
---
## Step6.
**Autoprefixer 패키지 설치**  
Autoprefixer 패키지 설치 후 webpack.config.js 수정
~~~bash
npm i -D postcss autoprefixer postcss-loader
~~~
~~~javascript
module.exports = {
    ...,
    module: {
        rules: [
            {
                test: /\.s?css$/,
                use: [
                    ...,
                    // sass위에 적어야 함
                    'postcss-loader',
                    'sass-loader'
                ]
            },
        ]
    },
    ...
}
~~~

package.json 에 borwerslist 추가
~~~json
"browserslist": [
    "> 1%",
    "last 2 versions"
]
~~~

.postcssrc.js 파일 생성 후 내용추가
~~~javascript
module.exports = {
    plugins: [
        require('autoprefixer')
    ]
}
~~~
---
## Step7.
**Babel 적용**
~~~bash
npm i -D @babel/core @babel/preset-env @babel/plugin-transform-runtime
~~~

.babelrc.js 파일 생성 후 구성 옵션 정리
~~~javascript
module.exports = {
    presets: ['@babel/preset-env'],
    plugins: [
        ['@babel/plugin-transform-runtime']
    ]
}
~~~

webpack.config.js 파일 수정
~~~javascript
module.exports = {
    ...,
    module: {
        rules: [
            ...,
            {
                test: /\.js$/,
                use: [
                    'babel-loader'
                ]
            }
        ]
    },
    ...
}
~~~

babel-loader 설치
~~~bash
npm i -D babel-loader
~~~
