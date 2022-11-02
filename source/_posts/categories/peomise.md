---
title: promise
date: 2022-11-02 08:24:49
type: "categories"
categories:
- JavaScript
- promise
tags:
- js
- promise
---

### promise
**primise是解决异步编程的一种方案，也是ES6统一了用法，promise是一个构造函数，用来生成promise实例**
* 优点
    - 可以将异步的操纵以同步的形式展现出来，避免了层层嵌套的回调函数，使代码更加优雅
* 缺点
    - 无法取消 Promise，一旦新建它就会立即执行，无法中途取消
    - 如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部
    - 当处于 Pending状态时，无法得知目前进展到哪一个阶段(刚刚开始还是即将完成)
* 参数
    - new Promise((==resolve==, ==reject==) => {}
      promise自己身上的方法作为参数:
        + resolve: 将promise的状态改为fulfilled（完成）
        + reject: 将promise的状态改为rejected（失败）
    - promise.then(res => {}, e => {})
        + onfulfilled: Promise状态为fulfilled，调用then的onfulfilled方法
        + onrejected: Promise状态为rejected，调用then的onrejected方法
* 状态
    - pending: 初始状态，既不是成功，也不是失败状态。
    - fulfilled: 意味着操作成功完成。
    - rejected: 意味着操作失败。
* 一个 Promise 只有这 3 种状态，且状态的转换过程有且仅有 2 种
    - pending到fulfilled
    - pending到rejected
### then / catch
```
// Promise新建后就会立即执行（执行new Promise）
let promise = new Promise((resolve, reject) => { // 同步宏任务
    console.log('123');
    resolve('resolve')
    // reject('reject')
    
    promise.then(
        onfulfilled => {
            console.log('onfulfilled', onfulfilled);
        },
        onrejected => {
            console.log('onrejected', onrejected);
        }
    )
    
    // 原型上的then,catch(常规写法)
    promise.then((val) => { // 微任务
        console.log('then', val);
    })
    promise.catch((val) => { // 微任务
        console.log('catch', val);
    })
})
```
### finally
finally()方法只有当状态变化的时候才会执行(即调用**resolve()或reject()**)，可以用来做一些程序的收尾工作，比如操作文件的时候关闭文件流。
```
let promise = new Promise((resolve, reject) => {
  // resolve('resolve')
  reject('reject')
})
promise.then(
  res => {
    console.log('res', res);
  },
  e => {
    console.log('e', e);
  }
)
promise.finally(() => {
  console.log('finally');
})
// e reject , finally
```
### all
Promise 的 all 可以将多个Promise实例包装成一个新的Promise实例。同时，成功和失败的返回值是不同的，成功的时候返回的是一个结果数组，而失败的时候则返回==最先==被reject失败状态的值
Promise.all方法的参数可以不是数组，但必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例。）
* 只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数
* 只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。
```
let promise1 = new Promise((resolve, reject) => {
      resolve('resolve1')
      // reject('reject1')
    })
    ⭐// p1会resolved，p2首先会rejected，但是p2有自己的catch方法，该方法返回的是一个新的 Promise实例，p2指向的实际上是这个实例。该实例执行完catch方法后，也会变成resolved，导致Promise.all()方法参数里面的两个实例都会resolved，因此会调用then方法指定的回调函数，而不会调用catch方法指定的回调函数。
    let promise2 = new Promise((resolve, reject) => {
      // resolve('resolve2')
      // reject('reject2')
      setTimeout(() => {
        reject('reject2')
      }, 1000)
    }).catch(e => {
      console.log('promise2, catch', e);
    })
    let promise3 = new Promise((resolve, reject) => {
      resolve('resolve3')
      // reject('reject3')
    })
    let promise = Promise.all([promise1, promise2, promise3])
    ⭐//这里加不会影响
    // promise2.catch(e => {
    //   console.log('promise2, catch', e);
    // })
    promise.then(
      res => {
        // 全是fulfilled状态--- res (3) ['resolve1', 'resolve2', 'resolve3']
        console.log('res', res);
      },
      e => {
        // 一个rejected状态--- e reject2
        console.log('e', e);
      }
    )
    // promise.finally(() => {
    //   console.log('finally');
    // })
```
### race()
Promise.race([p1, p2,p3])里面哪个结果==获得的快==，就返回那个结果，不管结果本身是成功状态还是失败状态。（使用场景基本没有）
