# Git



## 前言

* 免费、开源分布式版本控制系统

* 快速高效处理从小型到大型的各种项目

* 易于学习，占地面积小，性能极快

  

  **优缺点：**

  1. 集中式版本控制工具：
     优点：可看到项目中内容管理，管理员也可管理单一系统也比较容易。

     缺点：就是中央服务器的单点故障

2. 分布式的版本控制系统出现之后,解决了集中式版本控制系统的缺陷:
   1. 服务器断网的情况下也可以进行开发（因为版本控制是在本地进行的）每个客户端保存的也都是整个完整的项目（包含历史记录，更加安全）
   2. 工作机制
      工作区：通过写好的代码，放在了磁盘区，不是特指编译器中的代码
      临时存储：通过工作区git add放到此处（**让git追踪到代码**）
      本地库：通过临时存储的git commit放到此处
      远程库：通过本地库上传到远程库，代码托管中心是基于网络服务器的远程代码仓库





## 1.Git常用命令



| 命令名称                             | 作用           |
| ------------------------------------ | -------------- |
| git config --global user.name 用户名 | 设置用户签名   |
| git config --global user.email 邮箱  | 设置用户签名   |
| git init                             | 初始化本地库   |
| git status                           | 查看本地库状态 |
| git commit -m “日志信息” 文件名      | 提交到本地库   |
| git reflog                           | 查看历史记录   |
| git reset --hard 版本号              | 版本穿梭       |


下载他人的项目通过git clone 即可
有时候在github下的文件有点卡
需要进行端口配置（查看本机系统的配置）
之后window输入

`set http_proxy=http://127.0.0.1:7890`
`set https_proxy=http://127.0.0.1:7890`
如果是linux，set改成export

如果上传到自已的github上，直接跟着官网走就可

### 1.1 设置用户签名

通过使用如上的命令
截图如下

![image-20220222215251008](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220222215251008.png)

**签名的作用是区分不同操作者身份，以此确认本次提交是谁做的**
这里设置用户签名和将来登录 GitHub（或其他代码托管中心）的账号没有任何关系



### 1.2 初始化本地库

通过使用命令`git init`
初始化之后会生成一个隐藏文件

![image-20220223222524369](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220223222524369.png)

### 1.3 查看本地库状态

通过`git status`
首次查看与有文件提交时不一样的

首次查看

![image-20220223222906158](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220223222906158.png)

新增文件之后再次查看是不一样的状态
通过书写一个txt文件

![image-20220223222951547](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220223222951547.png)

**此处的文件还是红色的**
**说明是还没提交到缓存区**
**如果提交到缓存区就会变成绿色**

### 1.4 添加缓存区

通过`git add 文件名`
具体命令大致如下

![image-20220223223011043](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220223223011043.png)

如果要删除文件，通过rm进行删除
但是删除的只是缓存区
通过ll还可查看到存在

![image-20220223223032529](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220223223032529.png)

而且通过查看其状态的时候
git status发现为红色

还需要将其添加为添加到缓存区中



### 1.5 提交本地库

通过`git commit -m "日志信息" 文件名`
通过其git status也可看出其不同
而且查看日志也可看出版本更新
最后的截图如下

![image-20220223224205496](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220223224205496.png)



### 1.6 修改文件

修改文件之后，再次查看其状态
会检测到其文件已经被修改

![image-20220224191953598](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220224191953598.png)

之后再将其上传到缓存中

![image-20220224192007473](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220224192007473.png)



### 1.7 历史版本

`git reflog` 查看版本信息
`git log` 查看版本详细信息

![image-20220224192020428](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220224192020428.png)

#### 1.7.1 版本穿梭

本身默认查看使用cat 的时候，是最新版本
如果要穿梭到之前的版本，则需要通过`git reset --hard 版本号`
其日志的版本号，简易复杂版本的号字段有些不同
之后查看cat只是显示之前的版本

![image-20220224192050927](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220224192050927.png)

查看其之前的版本

![image-20220224192104558](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220224192104558.png)

Git 切换版本，底层其实是移动的 HEAD 指针



## 2.Git分支操作



​	在版本控制过程中，同时推进多个任务，为每个任务，我们就可以创建每个任务的单独分支。使用分支意味着程序员可以把自己的工作从开发主线上分离开来，开发自己分支的时候，不会影响主线分支的运行

**优点:**

1. 同时并行推进多个功能开发，提高开发效率

2. 各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败的分支删除重新开始即可



**分支常用命令**:

   | 命令名称            | 作用                         |
   | ------------------- | ---------------------------- |
   | git branch 分支名   | 创建分支                     |
   | git branch -v       | 查看分支                     |
   | git checkout 分支名 | 切换分支                     |
   | git merge 分支名    | 把指定的分支合并到当前分支上 |

查看分支可以通过`git branch -v`
*表示当前的分支路径

![image-20220224194107994](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220224194107994.png)

创建分支`git branch 分支名`

![image-20220224194129950](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220224194129950.png)

切换分支`git checkout 分支名`

![image-20220224194148198](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220224194148198.png)



**合并分支**：

1. **正常合并**：
   使用`git merge 分支名`
   新数据会直接进行覆盖旧数据
   
   ![image-20220225103035804](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220225103035804.png)
   
2. **冲突合并**：
   当前分支与另一个分支修改同一文件，系统无法处理，不知该保留哪个修改
   
   ![image-20220225103400482](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220225103400482.png)
   
   ![image-20220225103445395](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220225103445395.png)
   
   必须人为进行操作
   之后再提交到缓存区
   再提交到本地库（本地库不能带文件名，不然有歧义识别不出来哪一个）
   
   ![image-20220225103555094](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220225103555094.png)使用命令`git commit -m "merge ss" `（不用文件名)



![image-20220225103720083](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220225103720083.png)

**具体创建分支的原理是**
当前所在的分支，其实是由 HEAD 决定的。



## 3.Git团队协作机制



**团队内协作**：

![image-20220225152742078](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220225152742078.png)

**跨团队协作**：

![image-20220225152803211](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220225152803211.png)



## 4. GitHub 操作



远程仓库命令

| 命令名称                           | 作用                                                     |
| ---------------------------------- | -------------------------------------------------------- |
| git remote -v                      | 查看当前所有远程地址别名                                 |
| git remote add 别名 远程地址       | 起别名                                                   |
| git push 别名 分支                 | 推送本地分支上的内容到远程仓库                           |
| git clone 远程地址                 | 将远程仓库的内容克隆到本地                               |
| git pull 远程库地址别名 远程分支名 | 将远程仓库对于分支最新内容拉下来后与当前本地分支直接合并 |

起好别名之后
可以通过别名上传文件



克隆公共库时不用密码登陆，克隆会做如下操作：

1. 拉取代码
2. 初始化本地仓库
3. 给公共库创建别名（origin）



**团队内合作**
可以通过github内中的设置有个manger access进行邀请账户
之后受邀人通过复制该链接进行同意即可

受邀人克隆clone代码之后编辑在上传可以实现更改代码的模型

**跨团队合作**
通过代码的fork变成自已的，之后修改之后通过pull request请求发送
如果成功就会变成是你修改的



## 5.Idea集成Git



1. 配置Git忽略文件

   > 忽略那些与项目实际功能无关，如idea中创建新项目时产生的文件。
   >
   > 好处：把他们忽略掉能够屏蔽IDE工具之间的差异

   后缀名必须为.ignore，名字一般为git.ignore，文件内容为：

   ```text
   # Compiled class file
   *.class
   # Log file
   *.log
   # BlueJ files
   *.ctxt
   # Mobile Tools for Java (J2ME)
   .mtj.tmp/
   # Package Files #
   *.jar
   *.war
   *.nar
   *.ear
   *.zip
   *.tar.gz
   *.rar
   # virtual machine crash logs, see 
   http://www.java.com/en/download/help/error_hotspot.xml
   hs_err_pid*
   .classpath
   .project
   .settings
   target
   .idea
   *.iml
   ```

   需要在.gitconfig 文件中引用忽略配置文件

   ```text
   [user]
   name = manong
   email = yanjiuseng
   [core]
   excludesfile = C:/目录/git.ignore
   ```

   

2. 在idea中配置选项

   ![image-20220226204806923](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220226204806923.png)

3. 创建git本地库

   ![image-20220226205053167](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220226205053167.png)

4. 点击文件/工程进行git相关操作

   创建完成后，此时文件是红色表示未追踪

   ![image-20220226205228833](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220226205228833.png)

   可以点击文件/工程进行add操作

   <img src="https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220226205453473.png" alt="image-20220226205453473" style="zoom: 67%;" />

   commit操作也一样

   <img src="https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220226205826321.png" alt="image-20220226205826321" style="zoom:67%;" />

   提交完成后，颜色就会正常了。

   再次修改文件的话，颜色变蓝。

   点击 Version Control，然后点击 Log 查看版本，并且可以切换版本

5. 创建切换分支
   选择 Git，在 Repository 里面，点击 Branches
   或者idea右下角的git branch

   切换分支可以通过点击idea右下角的git branch中的check out

   **合并分支并且解决冲突**
   合并分支 同理也是idea右下角的git branch中的merge into current



## 6.Idea集成github

1. 使用idea中的github插件

   1. 一般使用token登陆

2. 在idea中将项目推送到github远程库中

   ![image-20220228202213437](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220228202213437.png)

   ![image-20220228202301075](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220228202301075.png)

   3. 修改代码后再推送，就直接在git选项中找到push选项

   4. 拉取

      ![image-20220228211457676](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220228211457676.png)

   5. 导入别人的项目

      ![image-20220228213615475](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220228213615475.png)

      输入对应项目链接

      ![image-20220228213647962](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220228213647962.png)



## 7.Idea集成gitee

同上，gitee支持将github的项目迁移到gitee上

