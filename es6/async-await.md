# async 函数
网站地址：[阮一峰es6](http://es6.ruanyifeng.com/#docs/async)
## 什么是async
ES2017 标准引入了 async 函数，使得异步操作变得更加方便。    
async 函数是什么？一句话，它就是 Generator 函数的语法糖。   
前文有一个 Generator 函数，依次读取两个文件。
    const fs = require('fs');

    const readFile = function (fileName) {
    return new Promise(function (resolve, reject) {
        fs.readFile(fileName, function(error, data) {
        if (error) return reject(error);
        resolve(data);
        });
    });
    };

    const gen = function* () {
    const f1 = yield readFile('/etc/fstab');
    const f2 = yield readFile('/etc/shells');
    console.log(f1.toString());
    console.log(f2.toString());
    };

写成async函数，就是下面这样。

        const asyncReadFile = async function () {
        const f1 = await readFile('/etc/fstab');
        const f2 = await readFile('/etc/shells');
            console.log(f1.toString());
            console.log(f2.toString());
        }; 
一比较就会发现，async函数就是将 Generator 函数的星号（*）替换成async，将yield替换成await，仅此而已。
## Promise对象状态化
async函数返回的 Promise 对象，必须等到内部所有await命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到return语句或者抛出错误。也就是说，只有async函数内部的异步操作执行完，才会执行then方法指定的回调函数

    async function getTitle(url) {
        let response = await fetch(url);
        let html = await response.text();
        return html.match(/<title>([\s\S]+)<\/title>/i)[1];
    }
    getTitle('https://tc39.github.io/ecma262/').then(console.log)
    // "ECMAScript 2017 Language Specification"
上面代码中，函数getTitle内部有三个操作：抓取网页、取出文本、匹配页面标题。只有这三个操作全部完成，才会执行then方法里面的console.log。
## await命令
正常情况下，await命令后面是一个 Promise 对象。如果不是，会被转成一个立即resolve的 Promise 对象。

    async function f() {
        return await 123;
    }
    f().then(v => console.log(v))

## 使用注意点
- await命令后面的Promise对象，运行结果可能是rejected，所以最好把await命令放在try...catch代码块中。

        async function myFunction() {
        try {
            await somethingThatReturnsAPromise();
        } catch (err) {
            console.log(err);
        }
        }

        // 另一种写法

        async function myFunction() {
        await somethingThatReturnsAPromise()
        .catch(function (err) {
            console.log(err);
        });
        }
- 多个await命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。

        let foo = await getFoo();
        let bar = await getBar();
上面代码中，getFoo和getBar是两个独立的异步操作（即互不依赖），被写成继发关系。这样比较耗时，因为只有getFoo完成以后，才会执行getBar，完全可以让它们同时触发。

        let [foo, bar] = await Promise.all([getFoo(), getBar()]);

        // 写法二
        let fooPromise = getFoo();
        let barPromise = getBar();
        let foo = await fooPromise;
        let bar = await barPromise;
上面两种写法，getFoo和getBar都是同时触发，这样就会缩短程序的执行时间。