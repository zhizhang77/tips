[TOC]

## windows下安装nodejs（注意这是基于原生windows而非wsl2上安装，wsl2的安装流程与linux基本一致）
### 直接安装
从https://nodejs.org/en/ 下载nodejs然后双击安装。建议完成后安装yarn：`npm install -g yarn`。

### 通过nvm安装
这是 https://docs.microsoft.com/en-us/windows/dev-environment/javascript/nodejs-on-windows 官方推荐的方法，但nvm for windows问题真的很多，非常不推荐。
1. 从 https://github.com/coreybutler/nvm-windows#node-version-manager-nvm-for-windows 下载nvm for wondows
2. 解压运行
3. 管理员模式打开powershell，输入
```powershell
nvm install latest # 安装最新版本nodejs，如果想装稳定版本则install lts
nvm ls # 显示已安装的nodejs版本号，比方说是17.3.0
nvm use 17.3.0 # 使用刚装的版本，注意nvm install并不会自动use新装的版本
npm --version # 检查npm是否正常工作及其版本号
npm install -g yarn # 安装yarn（可选）
```
如果nvm use出现问题可查看https://github.com/coreybutler/nvm-windows/wiki/Common-Issues#permissions-exit-1-exit-5-access-denied，做好找不到答案的准备，如果非要用nvm，windows11下的wsl2会是更好的选择。


## Linux下安装nodejs
与windows的主要区别在于nvm的安装使用，先到https://github.com/nvm-sh/nvm 下查看最新版本的nvm，假定是0.39.1，然后安装：
```sh
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
$ nvm install --latest # 注意这里要加--，而nvm for wondows则不用！
$ nvm ls # 显示已安装的nodejs版本号，比方说是17.3.0
$ nvm use 17.3.0 # 使用刚装的版本，注意nvm install并不会自动use新装的版本
$ npm --version # 检查npm是否正常工作及其版本号
$ npm install -g yarn # 安装yarn（可选）
```

## 设置淘宝镜像
`npm config set ELECTRON_MIRROR=https://npm.taobao.org/mirrors/electron/`
注意后面的/要加上，不加地址会变成 https://npm.taobao.org/mirrors/ 。另外有时候它没有跟主站及时同步会导致最新版本安装失败，这时候用@+版本号指定安装某个服务器上有的版本就行了，如`npm install -g electron@16.0.5`

## 入门示例
### electron
https://github.com/hokein/electron-sample-apps
https://www.electronjs.org/community#boilerplates
https://github.com/electron-userland/electron-webpack-quick-start
### electron+react
https://github.com/electron-react-boilerplate/electron-react-boilerplate (基于webpack)
https://electron-react-boilerplate.js.org/docs/app-showcase/
https://github.com/electron-userland/electron-webpack-quick-start
https://github.com/jschr/electron-react-redux-boilerplate (基于redux)

## npm组件集
### 基于react的json treeview
https://www.npmjs.com/package/react-json-view

使用时只要添加dependency，而后直接调用`<ReactJson src={JSON_OBJECT} />`即可。举例：假定我们用`npx create-react-app myproj`建立了名为myproj的目录，那么
1. 在$myproj/package.json$中添加上`"react-json-view": "latest"`（也可以直接安装`npm install -save react-json-view`，完成后npm会自动修改package.react-json-view
2. 如下修改$myproj/src/app.js$；
```jsx
import './App.css';
import ReactJson from 'react-json-view';

let my_important_json = {
    test: 'this is a test string',
    another_sibling: 45,
    basic_array: [1, 2, 3],
    how_will_floats_do: -2.757,
    parent: {
        sibling1: true,
        sibling2: false,
        sibling3: null,
        sibling4: 'test',
        'last-sibling': {
            grand_child: NaN,
            'grand-child-func': (a) => {
                let b = 5;
                return a.push(b);
            }
        }
    },
    number: 1234
}

function App() {
  return (
    <div className="App">
      <header className="App-header">
          <div style={{marginTop: 15}}>
              <div  className="modal" align="left">
                  <ReactJson src={my_important_json} theme="monokai" />
              </div>
          </div>
      </header>
    </div>
  );
}
export default App;
```
3. 启动npm即可生效
```sh
npm i
npm start
```

## FAQ
### npm安装出错
有时`npm install`没有提示问题，但`npm start`时则提示安装出错，找不到electron等。这时可以显式指定要装的模块并加上--verbose，如`npm install electron --verbose`，检查安装过程中的详细信息，方便分析问题。

## 打包
常用打包工具有[electron-builder](https://github.com/electron-userland/electron-builder)和[electron-packager](https://github.com/electron-userland/electron-packager)，都可以通过yarn安装：`yarn add electron-builder --dev` （注意yarn缺省也是local安装的，全局安装同样需要加-g开关）