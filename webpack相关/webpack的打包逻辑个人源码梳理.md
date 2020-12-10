##  webpack的打包逻辑个人源码梳理

###  理解基础的webpack.config.js文件

```javascript
const path = require('path')
module.exports = {
    context: __dirname,
    mode: 'development',
    devtool: 'source-map',
    entry: './src/index.js',
    output: {
        path: path.join(__dirname, './dist'),
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                use: ['babel-loader'],
                exclude: /node_modules/,
            }
        ]
    },
    plugins:[]
}
```

基础的几个点：

* entry:  入口文件
* module：理解为一个文件一个module
* loader：文件解析器
* plugin：各种插件，在各种钩子函数下挂载并且执行。
* chunk：代码块，理解为按需要分块



###  初步理解webpack执行流程

```javascript
const webpack = require('../lib/webpack.js')  // 直接使用源码中的webpack函数
const config = require('./webpack.config')
const compiler = webpack(config)
compiler.run((err, stats)=>{
    if(err){
        console.error(err)
    }else{
        console.log(stats)
    }
})
```

webpack有许多执行的方式启动，但最主要还是都通过webpack.js文件执行的。

下图是从网上拷贝的：

![img](https://user-gold-cdn.xitu.io/2019/10/16/16dd3c518ea12973?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



上方主要有几个主要点：      

解析1：hook钩子       

hook是webpack中最主要的地方，它在不同时期有各种不同的钩子，plugin插件就是注册在这些钩子上面的，当钩子函数执行的时候，就执行插件中的相应代码。



解析2：Compiler，Compliation    

Compiler可以理解构建整个打包流程的环境，Compliation负责具体的编译过程操作。



解析3：运行流程      

这里主要是webpack定义死的，会按照特定的钩子函数去执行对应的操作。

```javascript
Complier.beforeRun() // 执行对应的hook中的一堆plugin
Complier.run() // 执行对应的hook中的一堆plugin
Complier.make() // 执行对应的hook中的一堆plugin执行一堆plugin
Compliation.buildModule() // 执行对应的hook中的一堆plugin执行一堆plugin
...

```

介绍主要的流程前还要主要来看看webpack的`Tapable`库。



### Table库

这个库暴露了许多的Hook类，这里主要看一下插件plugin注册时注册的hook类，即SyncHook类



#### SyncHook

当plugin注册时有

```javascript
class SyncHook {
    constructor() {
        // 存放plugins
        this.taps = []  
    }
    // 添加plugin
    tap(name, fn) {
        item = Object.assign({}, {type: 'sync', name, fn})
        // 添加plugin
        this.taps.push(item)
        // 实际上，在push之前，还有会注册拦截器、对taps中的plugins进行排序等，这里只简单模拟下
    }
    // 执行plugins
    // 这个call，走的是SyncHook.js中的compile()
    call() {
        // factory.setup(this, options);
	// return factory.create(options);
	// 实际上后面走的callTapsSeries、callTap
	
    	// 模拟
        this.taps.forEach(tap => {  // 逐个plugin执行
            tap.fn()
        })
	
    }
}
```

就是一个订阅发布模式，来看看一个基础插件应该怎么写。



```javascript
class MyPlugin {
    apply(compiler) {   // 接收传过来的compiler实例
        compiler.hooks.beforeRun.tap('MyPlugin', function() {
            // 回调函数
            console.log(3333)
            file等操作...
        })
        // 可以注册多个不同的hook
        compiler.hooks.afterCompile.tap('MyPlugin', function() {
            // 回调函数
            console.log(444)
            file等操作...
        })
    }
}

```



当在webpack.config.js的plugin中new Myplugin实例，会在webpack函数中，判断options.plugin是否存在，并将plugin的实例依次执行apply将对应的回调函数挂载在对应的钩子函数上。这样等待webpack内部在特定的时期执行`hooks.beforeRun.call`就会依次执行挂载在当前钩子上的函数。



###   源码大致流程

这里列举从webpack函数到结尾大体流程。先来一张图看看



![img](file:///C:/Users/80302152/Desktop/16705be6123ef072.jpg)



这张图的将webpack的流程做了很好的概述



####  webpack(options,callbak)入口

```javascript
const webpack = (options, callback) => {
	......
  options = new WebpackOptionsDefaulter().process(options);
  compiler = new Compiler(options.context);
  compiler.options = options;
  
  ......
  
	if (options.plugins && Array.isArray(options.plugins)) {
		for (const plugin of options.plugins) {
			if (typeof plugin === "function") {
				plugin.call(compiler, compiler);
			} else {
				plugin.apply(compiler);
			}
		}
  }
  ......
  return compiler;
}
```

上面webpack方法主要是`new Compiler(options,context)`，也就是产生一个打包流程，在Compiler上挂上一堆hooks。之后返回，等待执行`run`



####  Compiler.run()

```javascript
// Compiler.js

run() {
    // NodeEnvironmentPlugin.js 中 beforeRun添加plugins
    this.hooks.beforeRun.callAsync(this, err => {
            // CachePlugin.js 中 beforeRun添加plugins 
            this.hooks.run.callAsync(this, err => {
                this.readRecords(err => {
                // 开始编译，重要
                    this.compile(onCompiled);
                });
            });
    });
    
    // 按文件搜索this.hooks.beforeRun.tap  就能找到在哪个js中添加的，其他插件同样这样搜
}
```

这里主要就是执行了beforRun钩子上的插件，和run的插件，最后执行compile编译传参onCompiled



####  Compiler.compile()

```javascript
	const onCompiled = (err, compilation) => {
		if (err) return finalCallback(err);

			if (this.hooks.shouldEmit.call(compilation) === false) {
				const stats = new Stats(compilation);
				stats.startTime = startTime;
				stats.endTime = Date.now();
				this.hooks.done.callAsync(stats, err => {
					if (err) return finalCallback(err);
					return finalCallback(null, stats);
				});
				return;
			}

			this.emitAssets(compilation, err => {
				if (err) return finalCallback(err);

				if (compilation.hooks.needAdditionalPass.call()) {
					compilation.needAdditionalPass = true;

					const stats = new Stats(compilation);
					stats.startTime = startTime;
					stats.endTime = Date.now();
					this.hooks.done.callAsync(stats, err => {
						if (err) return finalCallback(err);

						this.hooks.additionalPass.callAsync(err => {
							if (err) return finalCallback(err);
							this.compile(onCompiled);
						});
					});
					return;
				}

				this.emitRecords(err => {
					if (err) return finalCallback(err);

					const stats = new Stats(compilation);
					stats.startTime = startTime;
					stats.endTime = Date.now();
					this.hooks.done.callAsync(stats, err => {
						if (err) return finalCallback(err);
						return finalCallback(null, stats);
					});
				});c
			});
		};

// callbak即为上面的onCompiled
// Compiler.js
compile(callback) {
    const params = this.newCompilationParams();
    this.hooks.beforeCompile.callAsync(params, err => {
	this.hooks.compile.call(params);
	// 新建一个 compilation
	// compilation 里面也定义了 this.hooks ， 原理和 Compiler 一样
	const compilation = this.newCompilation(params);
	// 执行make函数，重要的
	this.hooks.make.callAsync(compilation, err => {
	compilation.finish(err => {
	    compilation.seal(err => {
	        this.hooks.afterCompile.callAsync(compilation, err => {
	            return callback(null, compilation);
	        });
	    });
        });
    });
}

```

这里主要执行了make的钩子函数，依次执行make钩子上注册的回调函数。这里注册的回调函数会执行

`compilation.addEntry(context, dep, name, callback);`      

makeHooks上绑定的为

```javascript
		compiler.hooks.make.tapAsync(
			"SingleEntryPlugin",
			(compilation, callback) => {
				const { entry, name, context } = this;

				const dep = SingleEntryPlugin.createDependency(entry, name);
				compilation.addEntry(context, dep, name, callback);
			}
		);
```



#####  Compilation.addEntry

addEntry主要就是执行了一个` _addModuleChain`函数

```javascript
// 主要执行的_addModuleChain方法
```



#####   _addModuleChain方法

```javascript

_addModuleChain() {
    moduleFactory.create(rsu => { // 打开 NormalModuleFactory.js 查看 create 方法
        // 收集一系列信息然后创建一个module传入回调
        // 执行this.buildModule方法方法，重要
        this.buildModule() // 见下方
    })
}

```

这里主要就是通过模块工厂创建



#####  buildModule

```javascript
buildModule(module, optional, origin, dependencies, thisCallback) {
    // 触发buildModule事件点
    this.hooks.buildModule.call(module);
    //！！！ 开始build，主要执行的是NormalModuleFactory生成的NormalModule中的build方法，中的build方法，打开NormalModule
    module.build(   // doBuild
        this.options,
        this,
        this.resolverFactory.get("normal", module.resolveOptions),
        this.inputFileSystem,
        error => {
            ......
        }
    );


```

这里也是主要执行build



#####  NormalModule.js下的build

```javascript
build() {
    // 先看执行的doBuild
    return this.doBuild(options, compilation, resolver, fs, err => {
        // 调用parse方法，创建依赖Dependency并放入依赖数组
        try {
        // 调用parser.parse
        const result = this.parser.parse(
            this._ast || this._source.source(),
            {
                current: this,
                module: this,
                compilation: compilation,
                options: options
            },
            (err, result) => {
                if (err) {
                    handleParseError(err);
                } else {
                    handleParseResult(result);
                }
            }
        );
        if (result !== undefined) {
            // parse is sync
            handleParseResult(result);
        }
    } catch (e) {
        handleParseError(e);
    }
    })
}
doBuild(options, compilation, resolver, fs, callback) {
    runLoaders(rsu => { // 获取loader相关的信息并转换成webpack需要的js文件，原理见下方链接
        callback()// 回build中执行ast(抽象语法树，编译过程中常见结构，vue、babel原理都有)
    })
}

```

主要说一下create创造module的流程，执行buildmodule,其中执行modle.build,即执行build方法，其中通过runLoaders执行解析相应文件，再生成AST语法树。       

最后遍历 AST，构建该模块所依赖的模块。





####  Compilation.seal

buildModule构建模块之后到Seal封装构建这一步，到这一布就是用来生成chunk的

```javascript
seal(callback) {
    // 触发事件点seal
    this.hooks.seal.call();
    
    // 生成chunk
    for (const preparedEntrypoint of this._preparedEntrypoints) {
        const module = preparedEntrypoint.module;
        const name = preparedEntrypoint.name;
        // 整理每个Module和chunk，每个chunk对应一个输出文件。
        const chunk = this.addChunk(name);
        const entrypoint = new Entrypoint(name);
        entrypoint.setRuntimeChunk(chunk);
        entrypoint.addOrigin(null, name, preparedEntrypoint.request);
        this.namedChunkGroups.set(name, entrypoint);
        this.entrypoints.set(name, entrypoint);
        this.chunkGroups.push(entrypoint);
    
        GraphHelpers.connectChunkGroupAndChunk(entrypoint, chunk);
        GraphHelpers.connectChunkAndModule(chunk, module);
    
        chunk.entryModule = module;
        chunk.name = name;
    
        this.assignDepth(module);
    }
    this.processDependenciesBlocksForChunkGroups(this.chunkGroups.slice());
    this.sortModules(this.modules);
    this.hooks.afterChunks.call(this.chunks);
 
    this.hooks.optimizeTree.callAsync(this.chunks, this.modules, err => {
        ......
        this.hooks.beforeChunkAssets.call();
        this.createChunkAssets();  // 生成对应的Assets
        this.hooks.additionalAssets.callAsync(...)
    });
    }

```

上面的具体我也看不懂。chunk大致生成算法

```javascript
1.webpack 先将 entry 中对应的 module 都生成一个新的 chunk
2.遍历 module 的依赖列表，将依赖的 module 也加入到 chunk 中
3.如果一个依赖 module 是动态引入的模块，那么就会根据这个 module 创建一个新的 chunk，继续遍历依赖
4.重复上面的过程，直至得到所有的 chunks
```



####   执行onCompiled回调，Compiler.done

最后一步，webpack 调用 Compiler 中的 emitAssets() ，按照 output 中的配置项将文件输出到了对应的 path 中，从而 webpack 整个打包过程结束。





#### 总结

上面的东西其实还不及webpack的皮毛，要想深入学习还是要看一下真正的具体执行代码，而不是像我这样看执行了什么函数，这个函数做了什么。这种方式只是过一下webpack流程，加深一下自己对webpack的内部compiler，compilation类的了解，理解chunk，module，bundle的概念。最主要的是清楚webpack的Tapbale概念，及挂载的钩子，插件挂载钩子的时机，及插件执行的时机。

