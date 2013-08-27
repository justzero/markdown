# 惠惠购物助手工程说明

## 工程结构
    .
    ├── Gruntfile.js
    ├── README.txt
    ├── dest
    ├── doc
    ├── local_tasks
    ├── node_modules
    ├── package.json
    └── src


## 工程介绍

###　 安装

本工程需要nodejs环境，依赖grunt,uglify,connect,watch等第三方包  

	npm install
	
### 环境介绍

- Gruntfile.js

	grunt得配置文件，里面定义了多种任务
	
- package.json

	项目得管理配置文件，包括项目名称，版本号，项目得包依赖关系
	
- node_modules
	
	nodejs得依赖包
	
- local_tasks

	本地得grunt自定义任务，主要包括打包任务，server得路由，以及开发模块自动化处理等任务
	
- src

	开发路径
	
- dest
	
	输出路径
	
- doc

	文档位置
	

### 运行

助手工程提供3中运行模式

注意： 尽量都是后端执行程序

- 本地调试模式（locate debug mode）

	后台运行本地server服务，访问127.0.0.1:8000/即可
	
		## start local server
		>> grunt default &
		.. or
		>> grunt &
		.. search http://127.0.0.1:8000/index.html
		
	

- 在线调试模式（online debug mode）

	后台运行server服务，在启动watch服务检查工程代码变动，重新打包代码
	
		## start local server
		>> grunt &
		.. listen http://1237.0.0.1:8000/index.html
		>> grunt debug &
		.. listen dev-file changed -- repack project
		….
		.. or
		….
		>> grunt & grunt debug &
		….
		
	注：此模式需要将插件得加载地址指向`http://127.0.0.1:8000/dest/script/{{ name }}_{{version}}`
	
- 内部测试模式（test mode）

	说明：该模式是给公司测试人员提供的接口
	打包一个请求指向`http://zhushou.huihui.cn/`的测试包，上传到tb098机器上  
	测试人员需要修改本地host，利用测试插件包测试助手相关功能
		
		## create test package, copy to test server
		>> grunt test

### 打包上线

	## online package
	>> grunt online
	.. output -->> /dest/**
	.. push /dest/ -->> CDN


