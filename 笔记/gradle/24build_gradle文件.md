## build.gradle 文件

- build.gradle 是一个gradle 的构建脚本文件,支持java、groovy 等语言。
- 每个project 都会有一个build.gradle 文件,该文件是项目构建的入口,可配置版本、插件、依赖库等信息。
- 每个build 文件都有一个对应的 Project 实例,对build.gradle 文件配置，本质就是设置Project 实例的属性和方法。
- 由于每个 project 都会有一个build 文件,那么Root Project 也不列外.Root Project 可以获取到所有 Child Project,所以在Root Project 的 build 文件中我们可以对Child Project 统一配置,比如应用的插件、依赖的maven 中心仓库等。
- ![img](24build_gradle文件.assets/1656577790049-c7aadc25-af96-4e67-aff5-3978aa0c181e.jpeg)build 文件中常见的属性和方法如下所示：