+++
date = '2026-01-30T20:55:18+08:00'
draft = false
title = 'Git_核心指令与工作流指南'
+++
    在大规模项目开发或团队协作中，Git 是管理代码逻辑、追踪历史记录必不可少的工具。它不仅能防止代码丢失，还能提供分支管理以支持并行开发。

#一、基础配置
	在安装 Git 后的首要任务是配置个人身份标识。这是因为 Git 的每一次提交都会关联这些信息：
Bash
git config --global user.name "YourName"   -->>设置全局用户名

git config --global user.email "yourmail@example.com"  -->>设置全局邮箱

git config --list  -->>验证配置

##二、核心工作流
Git 的操作逻辑可以概括为三个主要区域的转换：工作区 --> 暂存区 --> 本地仓库
1.初始化：使用 git init 使当前文件夹受 Git 管理。
2.追踪：使用 git add . 将修改后的文件存入暂存区。
3.提交：使用 git commit -m "commit message" 将暂存区内容永久保存到本地仓库。

##三、常用指令速查
  起步与同步
     指令               		作用
git clone <url>     	从远程仓库下载完整项目到本地
git status          	查看当前文件的修改状态（红字表示未追踪，绿字表示已暂存）
git pull            	从远程拉取最新代码并合并到本地
git push				将本地的存档推送至远程服务器

  分支管理（分支允许你在不影响主线代码的情况下开发新功能）
git branch           	查看分支
git checkout -b <name> 
git merge <name>        合并分支
git branch -d <name>	删除分支

  撤销与恢复	
git log --oneline        查看历史
git stash                暂存工作
git stash pop            回复暂存
git reset --soft HEAD^   撤销提交

##四、提交规范建议
     推荐格式---> 类型：描述
	feat：新增功能
	fix ：修复BUG
    docs: 文档修复
    refactor: 代码重构	

##五、博客发布流程
1. 本地预览与调试
 Bash 
 hugo server -D   -->  运行本地服务器（-D 参数确保草稿也能显示）
 hugo server 
2.  检查图片路径
 如果在文章中引用了图片，请确保图片已放置在以下位置之一：
放在 static/images/ 目录下，Markdown 引用路径为 /images/xxx.png。
3. 执行线上推送
本地预览满意后，使用Git将内容推送到Github Pages：
 Bash
1）git add .  -->> 将所有改动添加到暂存区
2）git commit -m 'feat :新增Git核心指令指南'  -->>提交存档，备注建议使用提交规范建议
3）git push     -->> 推送到远程Github仓库
4. 最终确认
推送完成后，访问绑定的域名，如:xxxxxx.cn

 
 





















