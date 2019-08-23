# git的一些常见操作和一些概念

1.git版本管理工具中的四个概念
	
	（1）Workspace:工作区
	（2）Stage:暂存区
	（3）Repository: 仓库区（本地仓库）
	（4）Remote： 远程仓库
	其中工作区和暂存区在各个不同的分支是共享的。
	远程库--origin：git给远程仓库默认名称是origin

2.git add添加文件到暂存区，git commit提交暂存区到仓库区

3，

	git checkout develop //切换到develo分支
	git branch develop  //新建分支develop
	
	git checkout -b develop  //创建并切换到该分支（切换前先commit提交）
	
	git branch -d 【branch】 //删除分支


4，git merge是最难理解的部分

	git merge 【branch】就是合并指定分支到当前分支
	
	分支合并理解：简单来说就是文件的合并，当历史线是直线的，假设只有master和
	develop，将develop合并到master分支上，假如历史线是直线，则其实直接就是将master指向当前develop分支并将head指向develop
	
	如果历史线是有分叉，假如develop分支开发修改txt文件，回到master分支继续修改了txt文件这样develop分支合并到master分支的时候就会报错，因为txt文件的部分不相同。则git命令会报错，等你手工修改完并进行git add和commit操作后，才会合并。
	提示：解决冲突后最好将develop分支直接删除，如果回去，develop分支还是错误的。



5，开发大体过程

首先一般只是修改远程仓库的开发线程develop
	
	1.首先clone
	执行git clone -b develop "git地址"
	-b是克隆时的指定分支
	
	2.进入克隆的文件，此时当前本地仓库只有一个分支develop
	git checkout -b next-dev
	创建并切换next-dev分支上
	
	3.之后愉快的进行开发git add .，git commit -m ""
	
	4.开发完成后我们可以将next-dev分支合并到本地develop分支上（切换分支前，要在next-dev上commit到本地仓库）
	（1）切换到本地develop分支：git checkout develop
	（2）本地develop分支更新：git pull origin develop（当你合并分支的时候，可能其它同事又提交了新的内容）
	（3）在本地develop分支上合并next-dev：git merge next-dev（若分支合并出现冲突，手动解决）
	
	5.为什么不直接在本地develop分支上开发，因为如果突然有bug要改的话。本地next-dev分支开发时，你只需提交当前修改并切换到本地develop分支上去修改就行，修改后切换next-dev继续开发。



6，常用命令

本地仓库关联远程仓库：git remote add origin "git地址"
取消关联：git remote remove origin

查看所有分支：git branch -a 
