# 该文件用于记录使用和学习git过程中遇到的问题，及相应的解决办法

- 2015-08-14

- 问题1：提交是出错，提示本地的master落后于远程仓库分支origin->master

- 分析：出现该情况是因为有新的数据推送到远程仓库，而本地的仓库还没同步新的数据，
       故此时git push会出错;

- 解决方法： 从远程仓库抓取新的数据，再与本地数据合并，最后push
		-- git fetch origin
		-- git pull
		-- git push

- 问题2：删除了原来的数据，并已经push到远程仓库，想找回原来被删除的数据

- 分析： git提供了恢复数据的功能，git每次提交都会产生一个提交（commit）对象，同
		时会有一个校验和（SHA-1）用来标识这个对象，commit对象包含一个指向暂存区
		的指针, 包含提交作者的相关信息。恢复数据相当于是撤消某个提交，从而使数据恢
		复到本次提交前的状态，或者理解为将HEAD指针向前移动到某个commit对象上;

- 解决方法： 先找到想要回退到的那个commit对象的SHA，再执行命令 git reset -hard commit-SHA
	  -- git reflog 
		-- git reset --hard commit-SHA
		 

