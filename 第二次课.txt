## 如何使用gitbash 或命令行同步GitHub
	
	注意:如果要推出cmd当前输入，使用CTRL+C

### 安装Git bash

	# 安装git的最新版本，目前我安装的是2.18的，一路默认下去即可。会在系统右键菜单上生成两个快捷菜单:git GUI 、 git bash.
	# 安装后git 会自动在系统环境变量的path（注意：不是环境变量中）路径中添加：C:\Program Files\Git\cmd
	# 安装后调用gitbash是有图标的，而我的ThinkPad重装后没有系统状态栏图标

### 如何使用git bash 在本地新建文件夹并将文件夹所有资源同步到线上服务器
	
	# 首先需要确保本地电脑的GitHub是与服务器上的账号相通的，所以要先同步

	# 运行`git init` 会在本地新建一个隐藏的git目录，这个目录就是用来记录所有的git操作的

	# 运行`git add .` 将本地做的所有修改新增等新增到暂存区（暂存区还在本地）

	# 运行`git commit -m "添加注释内容" ` 将add的所有文件提交到暂存区（还在本地，尚未与服务器连接）

	# 运行`git push origin master`  将暂存区的所有修改提交到服务器github中。但是如果你本地没有与服务器的账户打通（即第一步没完成），则会报错，错误信息如下：

		```$ git push origin master
			fatal: 'origin' does not appear to be a git repository
			fatal: Could not read from remote repository.

			Please make sure you have the correct access rights
			and the repository exists.
		```
		意思是没有授权访问远程资源库，请确保你有权力访问该库


### 如何使用git bash 或命令行从服务器端拉取资源库到本地，并在本地做修改后提交到服务器？

	. 首先要获取线上的库的地址：比如 `https://github.com/disanping/20180820cloneToLocal.git` 格式即为 `https://github.com/用户名/资源库名称.git`
	. 在需要落地的文件夹下使用git bash 并输入`git clone 线上地址库`，比如`git clone  https://github.com/disanping/20180820cloneToLocal.git`
	. 这样在本地就出来一个文件夹了，里面的文件即为线上同步过来的。这时操作就如同在本地修改提交步骤。试着在本地提交，push origin master 后过几秒，弹出一个让我登陆GitHub账户的界面。初步怀疑这是因为我下载了GitHub桌面版的原因，而不是网页版的GitHub的登录界面。我输入账户密码后，提交成功了！！！
	· 需要注意一个细节:从线上clone下来的库，需要通过`cd 子文件夹`进入到资源库文件夹再做git等其他操作
	. 那么意味着是不是本地线上就这么被打通了呢？我接下来到刚刚报错的本地文件夹下，再一次提交看是否能成功，因为本地文件夹还没有同步到服务器端过。结果发现还是一样。那总结出来就是：
		```
		从线上clone下来的资源库不需要线上线下账户打通就可以提交，前提是你能登陆线上的那个账户。

		然后线下新建的资源库是无法提交到线上的，而且它也没有提供一个登陆界面给你。
		
		所以接下来就来解决这个问题。
		```

### 如何让本地新建的资源库能成功同步到线上？
	. 使用 SSH -T git@github.com 时，被拒绝，表示连接不了，这是因为我还没新建SSH，所以要新建SSH。

### 在本地创建SSH的方法
	一下步骤同使用git及GitHub的若干种方式一文。是在我的博文中。

	1. 首先在本地用户名下看看有么有 `.ssh` 文件夹，如果有则看看有没有id_rsa和id_rsa.pub着两个文件，如果有则下一步，如果没有则生成：

		``` 	
			ssh-keygen -t rsa -C "disanping@gmail.com"
		```	

		如果是第一次使用GitHub，那一般是没有以上文件的。
		按3次回车，不用填任何密码。
		这样再在本地用户名下就能看到那两个文件了。

	2. 登陆GitHub，打开`accout setting`--> `ssh kes` 面板新增`add ssh key`，填写任意名称，在key文本框里粘贴 id_rsa.pub文件的内容，再点击add key 即可。

	这时可以再次使用`ssh -T git@github.com` 测试是否连接成功! 果然告诉我已经连接成功，但是并没有将所有权力赋予给我。那我还是测试下看是否能够将本地的提交到服务器上吧。再重复`git push origin master`发现还是不行

	那是因为虽然账户打通了，但是你没告诉GitHub你要将本地仓库提交到远程仓库哪个对应的库中啊，也就是我们还需在服务器端再新建一个库来并在本地绑定远程库再进行提交就可以了。

	3. 将远程库关联到本地库
		首先要在远程新创建一个库。
		创建库后，因为关联了SSH，所以创建一个没有readme.md的资源库后自动出现了3个选项：
		# 从这个仓库克隆新的仓库
		# 关联已有的本地仓库
		# 从另外的仓库中导入项目

		我们使用第二个，代码它也会告诉你，这是第一次的操作。以后就不需要添加 remote 、-u这些关键字了。

		然后使用如下命令：
		`git remote add origin 远程仓库地址` //比如https://github.com/disanping/20180820cloneToLocal.git
		`git push -u origin master` //第一次推送加上参数u,表示关联远程查看并且推送全部本地资源到远程库中

		过几秒后，再刷新线上的那个库，即可看到一已经成功同步!

		接下来的更改，提交就不需要添加关键字。

		本库对应的远程资源库地址为：https://github.com/disanping/20180820line-local-togather

	留下的问题：

	但是有没有直接能在本地将库提交到远程，并自动在远程这里创建一个相同的库名字的远程资源库呢?
