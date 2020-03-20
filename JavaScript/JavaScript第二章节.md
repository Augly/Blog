- script
    - async 表示立即下载脚本，不妨碍页面中其他下载资源，异步加载外部js脚本
    - charset 可以忽略
    - defer 延时下载，表示脚本可以延迟到文档完全被解析和显示之后再执行，只适用于加载外部地址
    
##### script使用方式
    直接嵌入
    <script>
        codeing
    </script>
    引入外部
    <script src="外部链接">

