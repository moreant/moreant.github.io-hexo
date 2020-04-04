---
title: 用async/await解决地狱回调
categories:
  - mix
tags:
  - 编程
date: 2020-04-04 16:47:28
---



微信的 API 经常会传入 success、fail 之类的回调函数，逻辑简单时还好，一旦复杂起来，简直就是`回调地狱`。还好小程序现在已经支持 async/await 了

<!-- more -->

## 基础知识

建议阅读以下文章了解基础知识，否则你可能会看不懂我在说什么

- [Javascript异步编程的4种方法](http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html)

- [async 函数的含义和用法](http://www.ruanyifeng.com/blog/2015/05/async.html)

- [Promise](https://www.liaoxuefeng.com/wiki/1022910821149312/1023024413276544)



## 回调地狱

什么叫回调地狱，这就叫回调地狱，总共有 两个`回调函数`，三个`Promise对象`嵌套，你找全了吗。

```javascript
// userInfos 是数据库中 userInfo 的集合
  setUserInfo(event) {
    wx.getSetting({
      success: (res) => {
        if (!!res.authSetting["scope.userInfo"]) {
          wx.getUserInfo({
            lang: 'zh_CN',
            success: (res) => {
              const userInfo = res.userInfo
              userInfo.role = '学生'
              wx.cloud.callFunction({
                name: 'getOpenId'
              }).then(res => {
                userInfos.where({ _openid: res.result.openId }).count().then(res => {
                  if (res.total === 0) {
                    userInfos.add({ data: event.detail.userInfo }).then(() => {
                      Toast.success('注册成功')
                      wx.reLaunch({
                        url: '/pages/my/my',
                      })
                    })
                  } else {
                    Toast.success('登录成功')
                    wx.reLaunch({
                      url: '/pages/my/my',
                    })
                  }
                })
              })
            },
          })
        } else {
          Toast.fail('请允许')
        }
      },
    })
  },
```



即使加上注释，可读性也很低

```javascript
  setUserInfo(event) {
    // 获取设置
    wx.getSetting({
      success: (res) => {
        // 如果有获取用户信息的权限
        if (!!res.authSetting["scope.userInfo"]) {
          // 获取用户信息
          wx.getUserInfo({
            lang: 'zh_CN',
            success: (res) => {
              const userInfo = res.userInfo
              // 在用户信息中添加角色
              userInfo.role = '学生'
              // 用云函数获取用户的 openid
              wx.cloud.callFunction({
                name: 'getOpenId'
              }).then(res => {
                // 查询数据库中是否已经用该用户的信息
                userInfos.where({ _openid: res.result.openId }).count().then(res => {
                  if (res.total === 0) {
                    // 没信息就添加
                    userInfos.add({ data: event.detail.userInfo }).then(() => {     
                      // 刷新页面
                      wx.reLaunch({
                        url: '/pages/my/my',
                      })
                    })
                  } else {
                    // 有信息就直接刷新页面
                    wx.reLaunch({
                      url: '/pages/my/my',
                    })
                  }
                })
              })
            },
          })
        } else {
          // 点了拒绝就提示一下
          Toast.fail('请允许')
        }
      },
    })
  },
```



但是如果使用 `async/await` 就会变成这样

```javascript
  async setUserInfo(event) {
    const setting = await this.getSetting()
    // 是否授权了用户信息
    if (!!setting["scope.userInfo"]) {
      const userInfo = await this.getUserInfo()
      // 补充角色
      userInfo.role = '学生'
      const url = '/pages/my/my'
      // 是否注册过
      if (!await this.isRegistered(_openid)) {
        // 没注册就插入一条
        await userInfos.add({ data: event.detail.userInfo })
      }
      wx.reLaunch({ url })
    } else {
      Toast.fail('请允许')
    }
  },
```

当然我顺势进一步的简化了某些逻辑，也忽略了分离的异步方法



## 分离嵌套

### 回调函数

先从最外层的  `wx.getSetting` 的回调函数开始改起

```javascript
setUserInfo(event) {
    wx.getSetting({
      success: (res) => {
        if (!!res.authSetting["scope.userInfo"]) {
          // 授权了信息
        } else {
          // 没授权信息
        }
      }
    })
  }
```

分离后

```javascript
async setUserInfo(event) {
    const setting = await this.getSetting()
    if (!!setting["scope.userInfo"]) {
        //授权了信息
    } else {
        //没授权信息
    }
}

getSetting() {
  return new Promise((resolve, reject) => {
    wx.getSetting({
      success: (res) => {
        resolve(res.authSetting)
      },
      fail: (res) => {
        reject(res)
      }
    })
  })
},
```



### Promise对象

```javascript
  async setUserInfo(event) {
    const setting = await this.getSetting()
    if (!!setting["scope.userInfo"]) {
      // Promise
      wx.cloud.callFunction({
        name: 'getOpenId'
      }).then(res => {
        // 接着怎么做
      })
        
    } else {
      //没授权信息
    }
  }
```

分离后

```javascript
  async setUserInfo(event) {
    const setting = await this.getSetting()
    if (!!setting["scope.userInfo"]) {
      const _openid = await this.getOpenId()
      // 接着怎么做

    } else {
      //没授权信息
    }
  },
  
  async getOpenId() {
    try {
      const res = await wx.cloud.callFunction({ name: 'getOpenId' });
      return res.result.openId;
    }
    catch (error) {
      return error;
    }
  },
```



## 改造结果

```javascript
  async setUserInfo(event) {
    const setting = await this.getSetting()
    // 是否授权了用户信息
    if (!!setting["scope.userInfo"]) {
      const userInfo = await this.getUserInfo()
      // 补充角色
      userInfo.role = '学生'
      const url = '/pages/my/my'
      // 是否注册过
      if (!await this.isRegistered(_openid)) {
        // 没注册就插入一条
        await userInfos.add({ data: event.detail.userInfo })
      }
      wx.reLaunch({ url })
    } else {
      Toast.fail('请允许')
    }
  },

  /**
   * 获取用户设置
   */
  getSetting() {
    return new Promise((resolve, reject) => {
      wx.getSetting({
        success: (res) => {
          resolve(res.authSetting)
        },
        fail: (res) => {
          reject(res)
        }
      })
    })
  },

  /**
   * 获取用户信息
   */
  getUserInfo() {
    return new Promise((resolve, reject) => {
      wx.getUserInfo({
        lang: 'zh_CN',
        success: (res) => {
          resolve(res.userInfo)
        },
        fail: (res) => {
          reject(res)
        }
      })
    })
  },

  /**
   * 获取 openid
   */
  async getOpenId() {
    try {
      const res = await wx.cloud.callFunction({ name: 'getOpenId' })
      return res.result.openId;
    }
    catch (error) {
      return error;
    }
  },

  /**
   * 是否已注册
   */
  async isRegistered() {
    const _openid = await this.getOpenId()
    try {
      const res = await userInfos.where({ _openid }).count()
      return res.total
    }
    catch (error) {
      return error
    }
  }
```

