## python爬虫基本库:
###  一、页面下载器
1. requests(必学)
2. scrapy
3. selenium+chrome + PhantomJS(抓取动态网页，不推荐)
4. Splash(抓取动态网页，推荐)
总结： 对于下载器而言，python自带的urllib就不要花时间去学了，学了就忘，直接requests能满足大部分测试+抓取需求，进阶工程化scrapy，动态网页优先找API接口，如果有简单加密就破解，实在困难就使用splash渲染

### 二、页面解析器
1. BeautifulSoup(入门级)
2. pyquery （类似jQuery）
3. lxml
4. parsel
5. scrapy的Selector (强烈推荐, 比较高级的封装，基于parsel)
总结： 其实解析器学习一个就够了，其他都不用学，直接学习scrapy的Selector 就行，简单、直接、高效.
### 三、其他工具
1. execjs ：执行js Python爬虫
2. pyv8: 执行js mac安装pyv8模块-JavaScript翻译成python
3. html5lib
