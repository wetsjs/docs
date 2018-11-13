# wets 小程序开发框架文档

该教程讲的是如何使用 **wets** 小程序开发框架来进行小程序的开发

# 第一个小程序项目

## 创建小程序

```bash
$ yarn config set registry 'http://r.npm.sankuai.com'
$ yarn create @wets/wets-app myapp
```

## 启动小程序

1. 编译小程序

```bash
$ cd myapp
$ wets start
```

2. 打开 **微信开发者工具**

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/6101ED3912ED75166BC4229F7755FF8A.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/7D5BBC1D308D31058486C9B4BE079DEA.jpg)

选择小程序项目

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/BB2CE718C53042BE5F78E6E009181593.jpg)

新建小程序项目，填入小程序的 AppID，更改项目目录

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/0CBE2F118DBE9872AF0522779FC5A8E4.jpg)

选择 `myapp/dist` 目录

## 小程序目录结构说明

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/33D0CED413A77E14528F7662BC28B9EA.jpg)

```bash
myapp

- dist # 该目录为编译后的小程序，里面的内容不要修改，会自动生成的
- src # 该目录为开发的目录
  - pages # 该目录为页面存放目录，通过 wets 命令可以创建页面，下面会讲到
    - home # 小程序的默认主页面目录
      - home.page.css # 主页面的样式
      - home.page.html # 主页面的模板，实际是wxml改了后缀
      - home.page.ts # 主页面的代码
  - app.ts # 小程序的入口
  - index.ts # wets打包时的入口文件，第二行表示主页面

```

**home.page.ts** 说明

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/30206B5FE566A035FD84563FA68E96B4.jpg)

该文件表示了一个完整的页面，包含样式、模板、配置和代码。

```js
// 页面的样式，使用precss https://github.com/jonathantneal/precss
import "./home.page.css";
```

```js
// 页面模板，实际是wxml
import "./home.page.html";
```

```js
// 页面的配置，详情参考https://mp.weixin.qq.com/debug/wxadoc/dev/framework/config.html page.json部份的内容
@Page.Conf({
  navigationBarTitleText: 'HomePage',
})
```

```js
// 页面对象，必须使用export，不可使用export default
export class HomePage extends Page {}
```

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/E1B7E92B73B92EAA6E566129B5885B3B.jpg)

经过 **wets** 处理后会创建如上图左侧 4 个文件，右侧就是小程序页面的注册函数。

## 在小程序中使用 jsx

创建一个新页面 **login**

```bash
# 使用jsx语法需要安装 @wets/wets-types 这个包
$ yarn add --dev @wets/wets-types
$ wets page add --template jsx login
# 如果提示 wets 命令不存在，可以执行下面的命令
$ yarn global add @wets/wets-cli
```

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/6C62725EC694DA4185C42D9DED7A6669.jpg)

在主页面中增加一个跳转到登录页面的按钮

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/EECECF7778DA8400E9078CDBBF99C86A.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/BBCFBD4D67D0D0A9182620D00D2C62BE.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/1A74780E5F2644AAA30CED641DEDDD2B.jpg)

一个循环遍历的例子

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/620093527303B5845515F72DFDC73CEB.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/72B038E73765E0CBB4D42DC13BE1DFB7.jpg)

> @wets/wets-tsx-loader 由 [@李续铖](https://wiki.sankuai.com/pages/viewpage.action?pageId=560196449) 同学开发

## 在小程序中使用 Redux

```bash
# 安装Redux支持包
$ yarn add --dev @wets/wets-redux redux-logger @types/node

# 创建redux的modules目录
$ mkdir -p src/redux/modules
```

创建 **src/redux/redux/modules/login.ts** 文件

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/67645E2A15CD31229C8C188240ED2F9B.jpg)

```js
const initalState = {
  username: "",
  password: ""
};

export default (state = initalState, action: any) => {
  switch (action.type) {
    case "CHANGE_USERNAME":
      return {
        ...state,
        username: action.payload
      };
    case "CHANGE_PASSWORD":
      return {
        ...state,
        password: action.payload
      };
    default:
      return state;
  }
};
```

创建 **src/redux/reducers.ts** 文件

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/393C33A70F4505341F9180CC3E5D4B6E.jpg)

```js
import { combineReducers } from "redux";

import login from "./modules/login";

export default combineReducers({
  login
});
```

创建 **src/redux/configureStore.ts** 文件

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/1D1A8ED7F1AC13A7D80A9AF388CD1FD0.jpg)

```js
import { createStore, applyMiddleware } from "redux";

import reducers from "./reducers";

export default function configureStore() {
  const middleware = [];
  if (process.env.NODE_ENV !== "production") {
    const { createLogger } = require("redux-logger");
    middleware.push(createLogger());
  }
  return createStore(reducers, applyMiddleware(...middleware));
}
```

修改 **app.ts**

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/8AF985482F458DE5E3CBCB537A55795D.jpg)

```js
import { App } from "@wets/wets";
import { Provider } from "@wets/wets-redux";

import configureStore from "./redux/configureStore";

const store = configureStore();

@App.Conf({
  window: {
    backgroundTextStyle: "light",
    navigationBarBackgroundColor: "#0F1012",
    navigationBarTextStyle: "white",
    backgroundColor: "#EFEFF4"
  }
})
@Provider(store)
export class MyApp extends App {}
```

这样就配置完了，接下来就可以像在 **React** 中使用 **Redux** 一样了

修改 **src/pages/login/login.page.tsx**

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/9325D7B269AB8363C9B029B94E189D5D.jpg)

```jsx
import { Page } from "@wets/wets";
import { connect } from "@wets/wets-redux";
import "./login.page.css";

@Page.Conf({
  navigationBarTitleText: "LoginPage"
})
@connect((state: any) => state.login)
export class LoginPage extends Page {
  onUsernameChange(event: any) {
    this.props.dispatch({
      type: "CHANGE_USERNAME",
      payload: event.detail.value
    });
  }
  onPasswordChange(event: any) {
    this.props.dispatch({
      type: "CHANGE_PASSWORD",
      payload: event.detail.value
    });
  }
  render() {
    return (
      <view className="login-page">
        <text>Login Page</text>
        <view style="height: 30px;" />
        <view>
          用户名:
          <input value={this.data.username} bindinput={this.onUsernameChange} />
        </view>
        <view>
          密码:
          <input value={this.data.password} bindinput={this.onPasswordChange} />
        </view>
      </view>
    );
  }
}
```

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/C8056D857755109FE7E6D372E5E88F82.jpg)

## 在小程序中使用 Mobx

提供了 5 个接口: **Provider、inject、observer、route、autorun**，其中 route 用于在 store 中结合 action 方便的发送 HTTP 请求，详情请见下面的**getTopicList 方法**

```bash
# 安装Redux支持包
$ yarn add --dev @wets/wets-mobx mobx

# 创建redux的modules目录
$ mkdir -p src/stores
```

创建 **src/stores/index.ts** 文件

```js
import { useStrict } from "mobx";
import Test from "./test";

useStrict(true);
const test = new Test();
const store = {
  test
};

export default store;
```

创建 **src/stores/test.ts** 文件

```js
import {
  observable,
  action,
  computed,
  runInAction,
  IObservableArray
} from 'mobx';
import { route } from '@wets/wets-mobx';

export type Topic = {
  title: string;
};

export default class Person {
  @observable random = Math.random();
  @observable topicList = [{
    title: '没有主题'
  }] as IObservableArray<Topic>;

  @computed
  get fullRandom() {
    return '帅帅的' + this.random;
  }

  @action
  changeRandom() {
    this.random = Math.random();
  }

  @action
  @route('/api/v1/topics', 'GET')
  * getTopicList(tab: string) {
    const query = {
      page: 1,
      limit: 10,
      tab
    };
    const ret = yield query;
    const topicList: Topic[] = ret.data;
    runInAction(() =>
      this.topicList.replace(
        topicList.map(item => ({
          title: item.title
        }))
      )
    );
  }
}
```

修改 **app.ts**

```js
import { App } from "@wets/wets";
import { Provider } from "@wets/wets-mobx";
import { route } from "@wets/wets-mobx";

import store from "../src/stores";

route.prototype.setUrlOption({
  protocol: "https",
  host: "cnodejs.org"
});

@App.Conf({
  window: {
    navigationBarBackgroundColor: "#fff",
    navigationBarTextStyle: "black",
    backgroundColor: "#fff"
  }
})
@Provider({
  store
})
export class MyApp extends App {
  store: any;
}
```

这样就配置完了，接下来就可以像在 **React** 中使用 **mobx** 一样了
创建 **src/pages/test/index.tsx** 文件

```jsx
import { Page } from "@wets/wets";
import { inject, observer, autorun } from "@wets/wets-mobx";

import "./test.page.css";
import TestStore from "../../stores/test";

interface IData {
  test: TestStore;
  fullRandom: string;
  tabIndex: number;
  tabArray: string[];
}

@Page.Conf({
  navigationBarTitleText: "TestPage"
})
@inject("test")
@observer()
export class TestPage extends Page<any, IData> {
  onLoad() {
    console.log(this.data);
    this.setData({
      tabIndex: 0,
      tabArray: ["ask", "share", "job", "good"]
    });
    autorun(() =>
      this.setData({
        fullRandom: this.data.test.fullRandom
      })
    );
  }

  test() {
    this.data.test.changeRandom();
  }

  getTopicList() {
    this.data.test.getTopicList(this.data.tabArray[this.data.tabIndex]);
  }

  bindPickerChange(e: any) {
    console.log("picker发送选择改变，携带值为", e.detail.value);
    this.setData({
      tabIndex: e.detail.value
    });
  }

  render() {
    return (
      <view className="test-page">
        <text>{this.data.fullRandom}</text>
        <button bindtap={this.test}>changeRandom</button>
        <view className="section">
          <view className="section__title">tab选择器</view>
          <picker
            mode="selector"
            bindchange={this.bindPickerChange}
            value={this.data.tabIndex}
            range={this.data.tabArray}
          >
            <view className="picker">
              当前选择: {this.data.tabArray[this.data.tabIndex]}
            </view>
          </picker>
        </view>
        <button bindtap={this.getTopicList}>get topic list</button>
        <view className="topicList">
          {this.data.test.topicList.map((topic, index) => {
            return (
              <view key={index}>
                {index}: {topic.title}
              </view>
            );
          })}
        </view>
      </view>
    );
  }
}
```

> 该插件由 [@周新凯](https://wiki.sankuai.com/pages/viewpage.action?pageId=560173181) 同学开发

## 使用 weui-wxss

```bash
$ yarn add --dev weui-wxss
```

新建 **src/app.css**

```css
@import "weui-wxss/dist/app.wxss";
```

修改 **src/app.ts** 加入下面的代码

```js
import "./app.css";
```

修改 **src/pages/login/login.page.tsx** 的 `render` 函数返回

```js
render() {
  return (
    <view className="login-page">
      <view className="weui-cells">
        <view className="weui-cell weui-cell_input">
          <view className="weui-cell__hd">
            <view className="weui-label">用户名</view>
          </view>
          <view className="weui-cell__bd">
            <input
              className="weui-input"
              value={this.data.login.username}
              bindinput={this.onUsernameChange}
            />
          </view>
        </view>
        <view className="weui-cell weui-cell_input">
          <view className="weui-cell__hd">
            <view className="weui-label">密码</view>
          </view>
          <view className="weui-cell__bd">
            <input
              className="weui-input"
              value={this.data.login.password}
              bindinput={this.onPasswordChange}
            />
          </view>
        </view>
      </view>
    </view>
  );
}
```

最终效果

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/8CD345266772EFA572116CC467192311.jpg)

## 在小程序中使用 GraphQL

接下来我们要完成一个登录的效果

为了快速进行 GraphQL 服务的开发，我选择在 [https://www.graph.cool](https://www.graph.cool) 上建立一个测试的项目。

第一步登录 [https://www.graph.cool](https://www.graph.cool) ，进入 **console** 。

第二步创建 **FUNCTION**
![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/6178A66BEA8AE691F9513E7C170D51F3.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/B67B8D52F6A68E56FDFE249ADE3D7C66.jpg)

```
type LoginPayload {
  token: String!
}

extend type Mutation {
  login(username: String! password: String!): LoginPayload
}
```

```js
module.exports = function login(event) {
  const data = event.data;
  if (data.username === "admin" && data.password === "admin") {
    return {
      data: {
        token: "true"
      }
    };
  }
  return {
    error: "登录失败，用户名或密码不正确！"
  };
};
```

第三步测试

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/3FFE01D8D05273ABC5ED15D2485C0560.jpg)

点击左侧下方的 **PLAYGROUND**

```
mutation {
  login(username: "admin", password: "admin") {
    token
  }
}
```

测试输入错误的用户名密码

```
mutation {
  login(username: "admin", password: "admin123") {
    token
  }
}
```

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/C7DD4A409C12BB32A78BA71E934A709E.jpg)

至此，GraphQL 服务就完成了。

接下来是在小程序中使用 GraphQL 了

```bash
$ yarn add --dev @wets/wets-graphql
```

修改 **src/app.ts**

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/FC1828DE4CC1B286CD98888F4DC6A7F9.jpg)

```js
import { App } from "@wets/wets";
import { Provider, Client, createNetworkInterface } from "@wets/wets-graphql";

import configureStore from "./redux/configureStore";

import "./app.css";

const store = configureStore();

const networkInterface = createNetworkInterface({
  url: "https://api.graph.cool/simple/v1/cj93kov7o176g0125q2helcni"
});

const client = new Client({
  networkInterface
});

@App.Conf({
  window: {
    backgroundTextStyle: "light",
    navigationBarBackgroundColor: "#0F1012",
    navigationBarTextStyle: "white",
    backgroundColor: "#EFEFF4"
  }
})
@Provider({
  store,
  client
})
export class MyApp extends App {}
```

修改 **src/pages/login/login.page.tsx**

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/C542EADF67C0BE66A062692915E63B98.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/007E175AAFB76418663D249C6BBABE34.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/8D8E730A19AD4543DF684F12DBD486A9.jpg)

```js
import { Page } from "@wets/wets";
import { connect } from "@wets/wets-redux";
import { graphql, gql } from "@wets/wets-graphql";
import "./login.page.css";

@Page.Conf({
  navigationBarTitleText: "LoginPage"
})
@connect((state: any) => state.login)
@graphql(gql`
  mutation login($username: String!, $password: String!) {
    login(username: $username, password: $password) {
      token
    }
  }
`)
export class LoginPage extends Page {
  onUsernameChange(event: any) {
    this.props.dispatch({
      type: "CHANGE_USERNAME",
      payload: event.detail.value
    });
  }
  onPasswordChange(event: any) {
    this.props.dispatch({
      type: "CHANGE_PASSWORD",
      payload: event.detail.value
    });
  }
  login() {
    const { username, password } = this.data;
    this.props
      .login({
        variables: {
          username,
          password
        }
      })
      .then(() => {
        wx.showToast({
          title: "登录成功"
        });
      })
      .catch(() => {
        wx.showModal({
          title: "提示",
          content: "登录失败"
        });
      });
  }
  render() {
    return (
      <view className="login-page">
        <view className="weui-cells">
          <view className="weui-cell weui-cell_input">
            <view className="weui-cell__hd">
              <view className="weui-label">用户名</view>
            </view>
            <view className="weui-cell__bd">
              <input
                className="weui-input"
                value={this.data.username}
                bindinput={this.onUsernameChange}
              />
            </view>
          </view>
          <view className="weui-cell weui-cell_input">
            <view className="weui-cell__hd">
              <view className="weui-label">密码</view>
            </view>
            <view className="weui-cell__bd">
              <input
                className="weui-input"
                value={this.data.password}
                bindinput={this.onPasswordChange}
              />
            </view>
          </view>
        </view>
        <view className="weui-btn-area">
          <button className="weui-btn" type="primary" bindtap={this.login}>
            登录
          </button>
        </view>
      </view>
    );
  }
}
```

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/86166CB0C59AEB244B32A59E93E4C642.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/5310E261A910E3D21F01E8EE29D7C379.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/E4811F7B053BC0A225AB34C121762CFD.jpg)

![IMAGE](https://raw.githubusercontent.com/wetsjs/docs/master/resources/5563E962F8B97CF6635FAF5FFD79FAE5.jpg)

至此就完成了登录的功能了。

# 高级应用

## wets.config.js

该文件是 wets 的配置文件，目前支持 **webpack** 的配置。

默认情况下项目目录中不会创建 **wets.config.js** 文件，可以手动在项目目录中创建该文件

```bash
$ vim wets.config.js
```

```js
module.exports = {
  webpack: config => {
    return config;
  }
};
```

这样就可以配置一个 **webpack** 的插件了。

```js
const webpack = require("webpack");

module.exports = {
  webpack: config => {
    config.plugins.push(
      new webpack.DefinePlugin({
        "process.env": {
          APP_ENV: JSON.stringify(process.env.APP_ENV || "local")
        }
      })
    );
    return config;
  }
};
```

我想要处理 **svg** 图片时就可以添加一个 loader

```js
const webpack = require("webpack");

module.exports = {
  webpack: config => {
    config.plugins.push(
      new webpack.DefinePlugin({
        "process.env": {
          APP_ENV: JSON.stringify(process.env.APP_ENV || "local")
        }
      })
    );
    config.module.rules.push({
      test: /\.(svg)$/,
      use: [
        {
          loader: "url-loader",
          options: {
            limit: 8192
          }
        }
      ]
    });
    return config;
  }
};
```

在 **tsx** 中进行图片的处理的 loader

```bash
$ yarn add --dev @wets/wets-image-loader
```

```js
const webpack = require("webpack");

module.exports = {
  webpack: config => {
    config.module.rules[0].use.push({
      loader: "@wets/wets-image-loader",
      options: {
        limit: 8192
      }
    });
    return config;
  }
};
```

```js
render() {
  return (
    <image src={require('../../images/avatar.png')} />
  );
}
```

> 该 loader 由 [@周新凯](https://wiki.sankuai.com/pages/viewpage.action?pageId=560173181) 同学开发

# 由 wets 驱动的小程序项目

- [会员买单小程序](http://git.sankuai.com/projects/SJST/repos/fe.vip-wxapp/browse)
- [自餐点餐小程序](http://git.sankuai.com/projects/SJST/repos/fe.buffet-miniapp/browse)
