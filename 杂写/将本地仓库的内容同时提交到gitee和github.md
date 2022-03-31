# 将本地仓库的内容同时提交到GitHub和Gitee

## 前情提要

​		今天入职了新的公司，由于是从PHP转GO语言，肯定需要一定的时间去学习和适应。所以突然就有了记笔记的念头。想要将工作中或者自己学习的东西记录下来。

​		之前一直也有记笔记，使用的是有道云笔记，但是这个笔记软件使用起来一直不是特别喜欢，其实已经挑了好久的笔记软件了，但是还是没有特别满意的。所以考虑了好久，决定使用Markdown配合GitHub来进行笔记的编写和存储。

​		之前就有过使用Markdown配合GitHub来进行笔记编写和存储的想法，但是当时觉得有点麻烦所以没有实施行动。这次趁着换工作，换个心情，就搞起来。

​		选择这个组合有以下几个考虑：

- 使用笔记软件，笔记内容在本地是加密的，只能通过指定的软件打开才能读取。而md文件，我们只要使用md编辑器甚至是vscode，phpstorm等代码编辑器都可以进行阅读和编辑。
- markdown的书写方式相比于有道云笔记要舒服很多，而且排版简洁清晰，有道云笔记虽然也可以自己排版，但是我总是排着排着就发现乱套了。。。
- 可以在线查看阅读。
- 也是希望大家可以看见吧，笔记软件的笔记只能自己看，但是发到网上就不是这样了，大家都能看，和大家互相学习探讨。当初看人家写的技术博文有好多人看，自己也想写，但是从来没有行动，这次就算是行动一下吧，憧憬着有一天我的某一个仓库可以过100star吧，哈哈。

​		之所以选择GitHub和Gitee两个代码托管平台，也是因为国内GitHub访问实在是太不稳定了，时不时就连不上，而且又不是随时随地能搭梯子，所以就想着在Gitee也保存一份，所以这个平台不如GitHub出名，但是是国内的，访问速度很快。



## 操作流程

​		例如，我们要建立一个golang的学习笔记仓库，并同时推送到GitHub和Gitee

- 首先，我们要在本地生成SSH密钥，并配置到GitHub、Gitee

​		我们可以使用如下命令生成SSH密钥：

```
$ ssh-keygen -t ed25519 -C "your_email@example.com"
或者
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

​		生成密钥后，一般默认都是在你的家目录下有一个 `.ssh` 文件夹，我们要将里面的 ` id_rsa.pub`文件中的内容，复制并配置在托管平台对应的位置。

> GitHub：点击右上角头像 -> setting -> SSH and GPG keys -> new ssh key
>
> Gitee : 右上角头像 -> 设置 -> SSH 公钥

> 我在这里也碰到了一个问题，起因是我使用的是公司电脑，公司的代码也是存储在阿里云的托管平台的，所以事先就在本机上已经生成了ssh key，生成方式是：
>
> > ssh-keygen -t rsa -C "saipengxin@163.com"
>
> 但是将这个ssh key 配置在github后，克隆仓库的时候报错：
>
> >  You’re using an RSA key with SHA-1, which is no longer allowed. Please use a newer client or a different key type.
> > Please see https://github.blog/2021-09-01-improving-git-protocol-security-github/ for more information.
>
> 后来使用了github文档中提供的第二种方式进行生成就可以了。（就是上面两个命令中的第二个）

- 然后，在两个平台都建立代码存储仓库

  ![image-20220330233254061](assets/image-20220330233254061.png)

- 我们在本地使用git clone 命令将github的仓库个克隆下来。

  跟着一起下来的，不只有代码仓库，还有一个隐藏文件夹 .git ,Linux和Mac使用 `ls -n` 查看，Windows点击上面导航栏中的`查看-隐藏的项目`勾选上。



- .git 中有一个config文件，我们要对这个文件进行改动

```
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	symlinks = false
	ignorecase = true
[remote "origin"]
    # github 仓库地址 这行代码是本来就有的 指向github仓库
	url = git@github.com:saipengxin/GitNode.git
	# 新增 gitee 仓库地址 这行代码是新增的，指向gitee仓库地址
	url = git@gitee.com:saipengxin/git-node.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
	remote = origin
	merge = refs/heads/main
```

- 改动完成后，我们可以使用 `git remote -v` 命令来看当前仓库关联的远程仓库地址

![image-20220331100155456](assets/image-20220331100155456.png)

- 测试一下

![image-20220331100259776](assets/image-20220331100259776.png)

![image-20220331100420321](assets/image-20220331100420321.png)

![image-20220331100814191](assets/image-20220331100814191.png)



## 总结

​		其实写下这篇文章的时候，已经是将这套流程鼓捣完了并且有过简单的使用了。说实话，感觉并没有想象中那么好，主要有以下几个原因：

- 每次写完笔记都需要推拉，操作不是很便捷。
- 每在一个新设备上拉取git仓库后，都需要手动改一下`.git/config`文件中的内容，否则只会指向一个仓库地址。
- 移动端不是特别的友好
- 开发环境要配置好，例如我使用typora进行Markdown编写，那么图片的存储就是一个要配置的地方，只有每个设备的配置相同才能保证文章不会乱。如果有一个设备使用了不是相对路径，那么就可能会导致提交到仓库后图片显示不出来。

​		不过想来想去还是决定使用这种模式继续记录笔记和内容，一来是想将内容发出去大家都可以看见，这样就能一起学习进步，同时期待着自己遥遥无期的100star仓库，另外就是想，新公司，新环境换个新的心情吧。