# git and github tutorial

---
revision: (随着我使用的深入, 会更新更多功能和技巧)
2023年8月18日11:26:35: 第一版.

---
## 1. 版本控制的历史与基础知识
1. 版本控制系统VCS, Version Control System.
2. Linux的版本控制历史: 最初都是linus他自己手动合并代码, 后来BitMover公司出于人道主义精神, 授权Linux社区免费使用他们公司开发的BitKeeper版本控制软件, 但是这个公司要求不能破解他们的软件. 但是Linux社区不像商业社区那样对于参与开发的人约束那么强, 就有人破解了这个软件, 后来被发现了, 人家就不让用了, 然后Linus一咬牙一跺脚就自己用C语言自己开发了一个分布式版本控制系统git, 主体程序开发只用了两周的时间, 一个月后Linux系统的代码由git管理(2005年). 后来2008年, github上线了(现在由于github的存在, 以后在版本控制方面肯定用git的人越来越多).
3. 版本控制软件的类型:
   - 分布式版本控制centralized: 每个本地都有一份完整的数据, 所有历史版本在本地都有, 那么它的版本控制可以直接在本地完整的进行. 当需要协同开发时, 会使用一个远程仓库进行. 代表就是git和github.
   - 集中式版本控制distributed: 版本控制在远程服务器上进行, 每个本地只保存了某个历史版本, 你写好往服务器上提交就可以了. 所以你的电脑一定要联网才能进行版本控制. 方便进行协同开发. 代表就是SVN.
4. 版本控制软件不仅保存目录和文件的当前版本, 还能保存每一个提交的历史版本. 在保存每一个版本的内容的时候, 肯定不能保存那些重复的内容啊, 不然太浪费空间了. 只保存改动的部分. 这方面SVN采用的是**增量式管理**的方式, 而git采取的是**文件系统快照**的方式.

---
## 2. git基础
1. git命令跟Linux命令都是兼容的, 在git bash中也可以执行Linux命令.
2. 要使用git进行版本控制, 首先要配置你的个人信息, 因为要告诉git你是谁, 这样在版本log里面, git就会显示某个版本是谁生成的; 另外你要团队协作, 肯定也要知道谁是谁啊. 只需要配置**用户名**和**邮箱**这两个信息, 最好和github上的用户名和邮箱一样. 该信息被存在一个名为`.gitconfig`的文件中. 使用下面两条命令即可完成配置:
`git config --global user.name "TwZ"`
`git config --global user.email "TwithZ@163.com"`
注: 为什么要加`--global`选项, 因为在Linux的一台主机上是可以登录好几个用户的啊. 如果你只想为当前用户设置个人信息, 把`--global`选项去掉就行了. (The Git config command can accept arguments to specify the **configuration level**. such as `local`, `global`, `system`. `local`is the default level)
3. git进行版本控制时分有3个区: 工作区, 暂存区, 本地库.
4. 库就是里面有很多版本, 也可以叫**版本库**. 在自己电脑里面存的是**本地库**, 在github上的是**远程库**.
5. git每个版本只是保留了修改的部分, 而相比上一个版本没有修改的部分就不存储. 内部使用链表的方式存储, 每次修改提交就是一个节点, 即每个版本就是一个节点, 这个节点通过指针指向上一个节点, 上一个节点是上上一次修改的部分, 而源节点是一份完整的数据. 所以我想rollback到某一个版本, 只需要找到修改节点, 然后通过其指针找到前一个版本, 就可以得到这一个版本. 
6. git在管理版本的时候是通过一个**HEAD指针**来完成的, 用这个指针来完成历史记录的**前进**和**回退**. HEAD指向的那个节点, 然后挨个往前回溯一直到源节点, 就可以得到一个HEAD指向的节点对应的版本. 就算你有分支, 也可以回溯啊. (原理就是从某个树枝一直回溯到树根, 就可以得到一个版本) (其实`git checkout`命令就是在跳转HEAD指针)
7. 主分支叫master, (主分支有时候也叫main分支, 如github上就叫main), 主分支是一切分支的起点和终点. 其它的分支可以自定义名称. (注: 2020年10月1日后, Github将所有新建的仓库的默认分支从master修改为main)
8. 你创建一个分支就是复制一份原工程, 但是它不会在文件夹里显示出来, 因为它根本没必要显示出来, 你在支流上操作, 然后提交之后, 再回到主流上, 然后合并就可以了, 因为你最终要得到的东西还是主流上的东西. **分支功能主要是用在多人同时协作开发时的情况. 分支就在不同开发者的工程之间建立了很好的隔离, 而各自开发完成之后再能很好的合并**.
9. 主分支上的代码都被默认为是一个完整的代码, 应该保证它始终是所有分支中最完整的. 所以都应该是把其他分支合并到主分支上. (合并分支时, 要先checkout到master, 再合并) (你合并完那些分支就可以使用命令把那些分支给删除掉了)
10. 在合并不同分支的时候, 有可能会有**冲突**, 因为可能修改了同一个地方, git不知道该怎么合并, 需要你手动处理一下.

---
## 3. git常用命令
1. 在项目文件夹里执行`git init`, 就会在项目文件夹里建一个`.git`文件夹, 这里面的内容就用于版本控制.
2. `git log`用来查看提交日志. 输出信息有: 提交人, 提交时间, 提交说明. 它是分好几行显示一次提交信息的, 当你提交的次数很多的时候, 这输出信息就太多了, 而且很多都没必要, 此时可以用`git log --oneline`来只显示提交说明. (git log只会显示当前版本以前的版本)
3. 还可以使用`git reflog`显示简洁的版本号, 还会显示你的rollback记录.
4. `git add .`或`git add --all`就是把工作区的新的改动加到暂存区中.
5. `git reset --hard 版本号`可以实现**rollback**到指定版本. (`git log`命令会显示每次提交的版本号)
6. `git status`显示工作区和暂存区的状态.
7. `git commit`可以**一次性**把**暂存区中的**所有修改提交到本地库中(所以如果你要提交修改, 必须先add到暂存区), 然后会自动打开默认编辑器(默认是vim)让你写提交说明. 如果你不想打开vim写提交说明, 可以直接用`git commit -m "提交说明"`来提交修改. (-m是因为message)
8. `git branch`可以查看当前所有分支, 当前所在分支前面会有一个*.
9. `git branch 分支名`, 来创建一个分支.
10. `git checkout 分支名` , 就可以在不同分支之间切换.
11. `git merge 分支名`, 就可以将这个分支和主分支合并.

---
## 4. 配置ssh远程连接
### 4.1. 远程连接介绍
- 访问远程仓库又两种方式(因为github仓库就有两种地址模式), 一种是http, 另一种是ssh. http需要密码, 你每次clone, push, pull都需要输入github账户的密码, 比较繁琐, 很少用; 而ssh不需要密码, 你只需要配置一次密钥对即可. 所以ssh方式远程连接github用的比较多.
- ssh是一种远程连接方式, 采用非对称加密算法, 一方持有私钥, 一方持有公钥.

### 4.2. git与github建立ssh联系的步骤
1. 生成密匙对. 在任意位置打开git bash, 输入`ssh-keygen`命令创建一个密匙对(输入后一路回车就行, 密码可以不设置).
2. 给github配置公钥. 找到`C:\Users\Administrator\.sshid_rsa.pub`文件, 用记事本打开, 复制里面的全部内容, 然后在网页登录github, 找到`settings->SSH and GPG keys->New SSH key`, 然后把复制的内容添加到Key那一栏就可以了, Title那一栏是用来给这个公钥起个名字的.
3. 测试是否成功. 在git bash中输入`ssh -T git@github.com`命令测试一下, 会报一堆提示, 看起来好像要出错, 最后问你*Are you sure you want to continue connecting (yes/no/[fingerprint])?*, 在后面输入yes回车就可以了(注意别输成y了), 然后就会提示*Hi TwithZ! You've successfully authenticated, but GitHub does not provide shell access.*, 就成功了.

注: 1. 如果上面第3条后还不行, 可以先不管它报什么错, 先试试clone一个仓库, 然后里面才问你, 你再输入yes, 应该就行了. (我感觉这应该是问你是否信任github这个服务器, 信任了它以后就不会再这样问你了); 2. 这里我挂了全局的梯子. 3. 我看的是B站上这个[教程](https://www.bilibili.com/video/BV1dV411G77N/?spm_id_from=333.337.search-card.all.click&vd_source=939818502857687a4a334c7a40d2c98a).

---
## 5. 使用git访问github
使用git访问github都只是访问其中的一个仓库, 而仓库是哪一个, 就用仓库的地址标识, 就是那个ssh的地址.
### 5.1. 将本地项目推送到远程仓库
1. 在github上新建一个空的仓库(记为test), README.md文件, 复制ssh地址.
2. 到本地我的tugithub文件夹中, 使用git bash, 执行`git clone ssh地址`. 得到一个叫tugithub/test的文件夹.
3. 进入tugithub/test文件夹, 把所有需要推送的项目文件都放到这个里面, 然后`git add .`, `git commit -m "message"`提交到本次仓库.
4. 在tugithub/test中用`git push origin main`进行推送.
注: 如果是clone的话, 就直接在一个文件夹(记为文件夹A)里执行`git clone ssh地址`即可, 它会把远程仓库的项目根目录(一个文件夹)搬到文件夹A中, 而文件夹A中就相当于多了一个项目文件夹, 其他啥也没有, 以后用git工作就全在项目根目录里即可. (在一个文件夹中执行`git clone 链接`命令会执行3个动作: <u>初始化本地仓库, 拉取代码, 创建别名</u>(就是给远程仓库创建一个叫origin(默认)的别名, 以后你在项目根目录里用git进行push的时候就用origin指定远程仓库地址, 即`git push origin main`)
### 5.2. 团队协作
怎么使用远程仓库进行协作呢? 其实啊远程仓库就始终是一个主分支, 然后同一个团队里的每一个人都可以对它进行push和pull, 而你第一次的clone和后续的pull就相当于建立了一个分支, 而你的push就相当于是合并了分支.
#### 5.2.1. 团队内协作:
- 自己先建一个本地库, 然后push到远程仓库里. 然后团队内的别人clone下来到本地, 然后我再改一改, 我再push到远程库. 然后别人再pull拉取改动, 就可以更新自己的本地库.
- 只有团队内的人才能对远程仓库push和pull.
#### 5.2.2. 跨团队协作:
- 别的团队的人不能直接对那个远程仓库push和pull, 他要想改动这个仓库, 只有先fork一下远程仓库, 弄一个自己的一模一样的仓库, 然后再把自己的远程仓库clone到本地, 然后再在自己的本地改, 改好了再push到自己的远程库, 然后要想给别的团队的仓库的话, 就需要给仓库的主人发一个请求, 然后主人审核, 审核之后就可以进行merge操作合并. 然后该团队的人都就可以pull了.
- 

---
## 6. 补充说明
- git中默认不能使用Windows复制粘贴的快捷键, 当需要复制粘贴的时候, 要使用鼠标右键的选项.
- **git工作流**: 保证master分支上都是稳定的版本, 因为那些是要用于实际生产的. 你开发就专门开发一个新分支, 用于写不太稳定的功能, 然后在这个分支上写来写去, 该来该去, 测试来测试去, 最后测试稳定了, 再合并到master上. (其实那些软件, 什么稳定版, 测试版也都是这样的. 稳定版就是master分支上的版本, 测试版就是其他开发分支上的版本)
- github上的仓库也是把本地库里的所有版本都完全复制来的.
- 大公司为了协同开发会使用github, 但是要租上面的仓库, 防止代码泄漏; 还可以使用gitlab, 这是一个软件, 可以自己买一台服务器, 然后用gitlab自己搭一个代码托管平台, 仅供自己公司使用.
- If you are having trouble remembering **commands** or **options** for commands, you can use Git help. There are a couple of different ways you can use the help command in command line:
`git command -help` - See all the available options for the specific command.
`git help --all` - See all possible command.
- 如果你的仓库中, 有的文件夹里面还有git仓库. 最好不要这样. 当我要往github上推送项目工程时, 可能会出现这样的情况, git会报submodule的问题, 我最好是给submodule给删掉算了.
- **关于文件大小**:
  - github单个文件推荐大小50MB, 超过50MB你push的时候会报warning;
  - github单个文件最大100MB, 超过100MB你push的时候会报error.
  - 往上放东西的时候, 最好把自己的文件都精简一下.
  - 工程文件, 有时候非常大(尤其是VS工程文件.vs, 真的是巨大!!!), 只把源文件放上去就行了.
  - 项目构建的中间文件, 比如VSCode单片机项目的build文件夹里面的东西, 放上去没什么用啊. (反正就是各种build文件夹吧, 里面的东西都可以删掉, 用户自己构建的时候就会自动生成了)
- 自己的项目文件夹(记作A)跟要上传到github的文件夹(记作B)分开, 这是两个不同的文件夹, 上传到github时的操作是, 把A中需要上传的部分复制一份到B中, 再把B给push上去.
- 最后看一下令人兴奋的消息:
  ```shell
  $ git push origin main
  Enumerating objects: 443, done.
  Counting objects: 100% (443/443), done.
  Delta compression using up to 8 threads
  Compressing objects: 100% (423/423), done.
  Writing objects: 100% (442/442), 36.41 MiB | 1.19 MiB/s, done.
  Total 442 (delta 39), reused 0 (delta 0), pack-reused 0
  remote: Resolving deltas: 100% (39/39), done.
  To github.com:TwithZ/MagicCubeRobot.git
     5b169b0..41b9707  main -> main
  ```
- 