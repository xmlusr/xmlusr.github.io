---
title: dredd study
date: 2019-06-18 21:40:54
tags:
- dredd
- apiary
categories:
- 测试
---
> 最近被要求学习利用[apirary](https://help.apiary.io)进行测试，验证api文档与程序的一致性。apiary中的本地API测试工具是[dredd](https://help.apiary.io/tools/automated-testing/testing-local/)，在此记录如何用它对apiary中的文档进行验证。

## 使用方法
1. 首先下载node，再用如下命令安装：
`npm install dredd --global`
通过`dredd --version`查看版本
2. `dredd init`
按照提示完成后生成dredd.yml，dredd.yml整个任务的配置项，包括要验证的文档名，使用hook的语言，生成的报告要发往的位置等等。
3. `dredd`
运行测试程序，即可看到结果，如果配置了aipary的`id`的话，也可在apiary的Test部分看到带有颜色标记的测试结果。 

## 生命周期
*由于要使用到`hook`,先介绍一下dredd的声明周期，便于知道何时使用hook。*
1. 解析apiary文档，报告解析错误或者警告，发现格式问题。
2. 预处理apiary文档，对URI中的属性、JSON体等进检查是否存在无效参数或缺少必要参数。
3. 生成HTTP的`transaction`,包括将head头、参数、url中的属性集成到一起。
4. 载入hooks
5. 运行测试
    + 运行`beforeAll`hook
    + 对于每一个`transaction`：
        + 依次运行`beforeEach`hook、`before`hook
        + 发送HTTP请求
        + 接受HTTP响应
        + 运行`beforeEachValidation`hook
        + 执行验证
        + 运行`after`hook、`afterEach`hook
    + 运行`afterAll`hook
6. 发送详细报告

## hook
*hook是dreed中的最重要的部分，dredd支持的多种语言的hook,hook可以通过hook在请求前、响应后、比较前、比较后对特定一个或所有的transaction进行数据的修改，具体来说有如下一些作用：*
1. 处理身份验证问题，比如`Authorization`
2. 在`transaction`之间传递数据，例如`GET`的值用于`PUT`
3. 修改`request`中的样例值
4. 改变预期的期望值
5. 在验证前修改`response`的值，以符合预期

### hook的类型及其执行时期
+ `beforeAll` 在整个测试运行之前执行，文档预运行之后
+ `beforeEach` 在每个transaction之前
+ `before` 在指定的transaction之前
+ `beforeEachValidation` 在每个transaction的response和文档中的预期对比验证之前
+ `beforeValidation` 在指定的transaction验证之前
+ `after` 在指定的transaction完成之后去
+ `afterEach` 在每个transaction完成之后
+ `afterAll` 在整个测试完成之后

## dredd的参数
*dredd运行时有一些参数可以指定一些特殊行为,例如：*
+ `--hookfiles, -f`指定hooks文件
+ `--names, -n`只列出request的名称，不运行，这个名称就是上面的`before``after`中传入的参数，用于指定具体的transaction。
+  `--only, -x`只运行指定的transaction
+ `--sorted, -s`指定transaction的执行顺序，顺序如下
: CONNECT, OPTIONS, POST, GET, HEAD, PUT, PATCH, DELETE, TRACE.


