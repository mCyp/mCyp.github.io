

[我的博客](https://mcyp.github.io/):bamboo:

如果只有一个master分支存放页面的话，那么一旦更换电脑，hexo所部署需要的文件就无法重现

警告：远程克隆之后，在部署前需要删除掉.deploy_git文件。
git删除命令操作  
``rm -rf .deploy_git``
或者手动删除
否则会出现冲突