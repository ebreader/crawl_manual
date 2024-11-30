# EBReader网络爬取电子书使用说明
## 安装
下载地址：https://plugins.jetbrains.com/plugin/download?rel=true&updateId=642178  
插件初次开发，正在审核中，插件市场里搜不到，可以直接从这个地址下载。  
## 重要前置说明
使用此功能的核心是正确使用“爬取表达式”  
爬取电子书的核心就是运用“爬取表达式”在一个页面上确定电子书的名称、章节列表、章节的内容。  
爬取快捷键为  ALT + f  
如需使用代理，请将IDE的代理设置正确即可，如下图：  
![image](https://github.com/user-attachments/assets/56f933e2-2e9e-47de-9632-a12d53d6c222)  

后续本文档将首先详细讲解“爬取表达式”，然后使用一个案例进行说明具体的使用方法。  
## 爬取表达式
### 核心概念  
1. 节点：此处的节点概念与html文档里的节点概念一样，直接使用标签名称来定义一个节点，html body a div等就是一个节点。  
2. 路径：指的自根节点起到目标节点经历的路径，路径中各个节点的分割符用  \`（是ESC下面那个按键上的字符)  表达。  
   例如：html\`body\`div 表示查找html下面的body下面的所有div节点。
3. 节点索引：表示一个节点在其父节点中的排列次序。  使用 [n] 表达，n>=0。
   例如：
   ```html
   <html>
     <body>
       <div>1  我的索引是0</div>
       <a>我的索引是1</a>
       <a>我的索引是2</a>
       <div>2  我的索引是3</div>
       <div>3  我的索引是4</div>
       <div>4  我的索引是5</div>
       <div>5  我的索引是6</div>
       <div>6  我的索引是7</div>
     </body>
   </html>
   ```
   你想选中上方的第二个div应该这样写：html\`body\`div[3]  
   选中上方所有div：html\`body\`div  
   选中上方第1和3 div：html\`body\`div[0,4]  
   选中上方第3至5的div：html\`body\`div[4-6]  
   选中上方第一个和第3至5的div：html\`body\`div[4-6,0]  
   就不一一列举了,总之[0]  [0-10]  [1,2,5,6,8-12,33,35-45]  都是合法的表达。  
4.节点属性匹配表达：进一步精确筛选节点，使用 (#属性名=正则表达式#)  

例如：  
   ```html
   <html>
     <body>
       <div>1  我的索引是0</div>
       <a href="wang">我的索引是1</a>
       <a href="liang">我的索引是2</a>
       <a href="liang">我的索引是2</a>
       <a href="liang">我的索引是2</a>
       <div>2  我的索引是3</div>
       <div>3  我的索引是4</div>
       <div>4  我的索引是5</div>
       <div>5  我的索引是6</div>
      <a href="wang">我的索引是2</a>
      <a href="wang">我的索引是2</a>
      <a href="wang">我的索引是2</a>
      <a href="wang">我的索引是2</a>
       <div>6  我的索引是7</div>
     </body>
   </html>
   ```
选择所有href=liang 的a标签 html\`body\`a\[1-4\](\#href=^liang$\#) ，后面的属性匹配可以写多个，例如  
html\`body\`a\[1-4\](\#href=^liang$\#)(\#name=^liang$\#)(\#class=^liang$\#)  
5. 节点数据别名定义：节点的所有属性、本节点的text以及其所有子节点的text都可以是节点数据，给这些数据定义别名使用 {}   
数据定义如下：  
属性数据：由具体的属性名界定  
本节点的text数据：由 # 表达  
本节点及其所有子节点的text：由 % 表达  
例如：  
   ```html
   <html>
     <body>
       <div>1  我的索引是0</div>
       <a href="wang">我的索引是1</a>
       <a href="liang">我的索引是2</a>
       <a href="liang">我的索引是2</a>
       <a href="liang">我的索引是2</a>
       <div>2  我的索引是3</div>
       <div>3  我的索引是4</div>
       <div>4  我的索引是5</div>
       <div>5  我的索引是6</div>
      <a href="wang">我的索引是2</a>
      <a href="wang">我的索引是2</a>
      <a href="wang">我的索引是2</a>
      <a href="wang">我的索引是2</a>
       <div>6  我的索引是7</div>
     </body>
   </html>
   ```
案例表达式： html\`body\`a\[1-4\](\#href=^liang$\#){name=href,content=#}  
上述表达式表示，选取1-4的并且href=liang的a标签，并把href属性重定义为name，把它的text定义为content。  
6. ID选择：表达式支持使用ID选择器，使用 # 表示id  
例如：
   ```html
   <html>
     <body id=“kkm”>
       <div>1  我的索引是0</div>
       <a href="wang">我的索引是1</a>
       <a href="liang">我的索引是2</a>
       <a href="liang">我的索引是2</a>
       <a href="liang">我的索引是2</a>
       <div>2  我的索引是3</div>
       <div>3  我的索引是4</div>
       <div>4  我的索引是5</div>
       <div>5  我的索引是6</div>
      <a href="wang">我的索引是2</a>
      <a href="wang">我的索引是2</a>
      <a href="wang">我的索引是2</a>
      <a href="wang">我的索引是2</a>
       <div>6  我的索引是7</div>
     </body>
  <body>
       <div>1  我的索引是0</div>
       <a href="wang">我的索引是1</a>
       <a href="liang">我的索引是2</a>
     </body>
   </html>
   ```
表达式： #kkm\`a\[1-4\](\#href=^liang$\#){name=href,content=#}  
表达式： html\`body[0]\`a\[1-4\](\#href=^liang$\#){name=href,content=#}  
上面两个表达式的效果是一样的



## 爬取案例
### 使用快捷键  ALT + f 调出爬取界面，如下  
![image](https://github.com/user-attachments/assets/98fda731-499a-43ee-98c8-e0bbe8f79917)  
#### 一.爬取一个电子书需要五个参数
1. 爬取地址：这里以https://ctext.org/shiji/ben-ji/zhs 地址为案例，爬取地址提供的页面上至少要能提供足够的章节信息。如下图：
   ![image](https://github.com/user-attachments/assets/e6371c70-3092-4582-abe6-c302e565ed91)  
2. 资源基址：是章节信息里提供的链接的相对地址，如果章节信息里提供的是绝对地址的话这里可以不用填写。在这个案例里基址是：https://ctext.org  
3. 书名选择器：在爬取页面上定位书籍名称的“爬取表达式”，在本案例中是：#content3\`table[0]\`tbody\`tr\`td[0]{name=%}  
   表达式解析：选择id为content3的节点下面的第0个table节点下面的tbody下面的tr下面的第0个td，将td 包括其所有子元素的text数据作为书籍名称，如下图：  
   ![image](https://github.com/user-attachments/assets/47701c6f-e547-44ba-a380-b1253cb62df3)
   在书名选择器的爬取表达式中使用name定义书籍名称数据。
   如果爬取页面中没有书籍名称也没关系，直接在名称选择器中以@开始，后面自己填写书名即可，例如
   ![image](https://github.com/user-attachments/assets/a8083125-f249-486e-831b-6357ad2a3575)
4. 章节选择器：是必须要正确书写的，本案例中是：#content3`a{name=#,link=href}，其中 name指定章节的名称，link指定章节的链接数据。  
   表达式解析：选择id为content3的节点下面的所有a节点，将a节点本身的text作为章节名称，href属性指定数据作为章节的链接。如下图  
   ![image](https://github.com/user-attachments/assets/4161a5e7-a989-4d7e-b0e7-1d059e961585)  
   章节的爬取默认是多线程爬取的，一个章节一个线程，如果想改为单线程模式，可以直接在资源基址前追加一个 # 符号即可，如下图所示  
   ![image](https://github.com/user-attachments/assets/ffe52fc0-e442-40de-9c27-51fef8b99b66)  
5. 章节内容选择器：也必须书写正确，这里是#content3\`table(#border=^0$#)\`tbody\`tr\`td(#class=^ctext$#){content=%}  
   表达式解析：选择id为content3的节点下面的border属性为0的table节点下面的tbody下面的所有tr下面的class属性为ctext的td节点，并将td本身及其所有子节点的text作为本章的内容。如下图所示  
   ![image](https://github.com/user-attachments/assets/20edf05a-69a8-4a03-9253-f3639a6ea488)
#### 二.爬取界面如下
##### 1.爬取中  
![企业微信截图_20241130151722](https://github.com/user-attachments/assets/002399b2-f8dd-4cdb-883b-df1da5960a28)
爬取日志会输出在下方的textarea中  
##### 2.爬取成功  
![企业微信截图_20241130151744](https://github.com/user-attachments/assets/5590e227-2105-4bd0-ab92-8368b4ce2cb4)
##### 3.爬取成功后并不会真正的保存这个电子书，你可以选择保存到你的阅读列表中，也可以选择导出。  
![image](https://github.com/user-attachments/assets/a299a3bf-be3a-44a7-b754-20a467044e5d)
##### 4.爬取参数还支持从一个properties文件中加载，其格式如下  
![image](https://github.com/user-attachments/assets/17cad487-3476-4302-88e1-3ff6ffeff2ba)  
## 有些复杂，遇到问题请联系作者
微信：laoduwu006  
邮箱：280202806@qq.com  
QQ群：905014508  
## 网路爬取说明至此完结，喜欢本软件的话，打赏作者鼓励持续更新维护
![企业微信截图_20241130115101](https://github.com/user-attachments/assets/ab66f392-fd61-4df5-85ac-270cc867ae5c)
![企业微信截图_20241130115141](https://github.com/user-attachments/assets/83b5d748-68b6-405f-8558-eaaa1e6ee917)
