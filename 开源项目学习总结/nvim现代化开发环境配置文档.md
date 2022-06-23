[toc]

# 现代化编辑器环境nvim配置

先展示一下我配置后的环境，如下：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1202325/1655954305671-93c2a9ea-d633-49c3-9497-bf1bac24aa77.png#clientId=u46c9451e-7bad-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=947&id=u86988fd0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=947&originWidth=1875&originalType=binary&ratio=1&rotation=0&showTitle=false&size=157689&status=done&style=none&taskId=uabd74912-db6b-45fc-a3f8-ffd2ede80dd&title=&width=1875)<br />当然这只是最基础的界面，更炫酷的页面可以自己去探索（修改配置）。O(∩_∩)O哈哈~，我们这里只讨论技术，不装逼。
<a name="BbDzO"></a>
## 安装nvim
由于公司仓库中`nvim`版本比较低，我们可以github下载最新安装包，手动去安装，然后将`vi`和`vim`映射为`nvim`, 如下是我的常用的linux命令的映射。
> alias vi='nvim'
> alias zshconfig="source ~/.zshrc"
> alias ohmyzsh="source ~/.oh-my-zsh"
> alias code= '/usr/share/code/code .
> --no-sandbox --unity-launch'
> alias cat='bat'
> alias ls='lsd -la'
> alias df='duf'
> alias fd='fdfind'
> alias top='htop'
> alias du='ncdu' 

<a name="PDgOO"></a>
## 克隆nvim配置
克隆[nvimdots](https://github.com/ayamir/nvimdots)这个github项目的配置，这是别人的一个nvim配置，里面提供了如下两种补全方案

- nvim-lspconfig+nvim-cmp
- coc

可以根据项目情况需要进行切换，这里推荐大家使用第一种解决方案，`lsp`可以配置多种语言的开发环境。
<a name="v2Mye"></a>
## 安装插件
在终端输入` nvim +PackerSync`会去自动安装`lua`脚本中配置的插件。<br />注：因为网络原因，有些插件可能安装不上，我们可以配置公司的代理，或者将`~/.config/lua/core/pack.lua`中的`git@github.com`替换成`git@gitee.com`, 然后再去安装。
<a name="YhLR8"></a>
## 依赖配置安装
 `nvim`主要使用插件去扩展其功能，而安装相关插件会依赖对应的环境。因此，我们需要安装下面的这些包。安装好上面的nvim配置后，在命令行模式下输入` :checkhealth`，就会弹出如下的检测页面，去检测对应插件的安装状态，我们可以根据对应插件的状态去安装对应的依赖包。![image.png](https://cdn.nlark.com/yuque/0/2022/png/1202325/1655953432128-a550cf45-c6e1-47c3-b5c4-4afac40cc07f.png#clientId=u46c9451e-7bad-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=843&id=u734788de&margin=%5Bobject%20Object%5D&name=image.png&originHeight=843&originWidth=981&originalType=binary&ratio=1&rotation=0&showTitle=false&size=82532&status=done&style=none&taskId=u28a77814-bc6e-4045-913f-bd1816087c4&title=&width=981)<br />安装好对应的依赖包后，我们重新启动下`nvim`, 将可以看到如下页面，这个页面可以根据自己的喜好进行自定义（修改对应lua脚本）。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1202325/1655954901412-39422e91-664d-4315-82dd-a533339d6def.png#clientId=u46c9451e-7bad-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=885&id=u8a1129d0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=885&originWidth=1039&originalType=binary&ratio=1&rotation=0&showTitle=false&size=37536&status=done&style=none&taskId=u929395ce-7332-4274-908b-0cec7ae995f&title=&width=1039)<br />然后在命令行模式下输入`：PackerSync`, 重新安装或者更新下对应的插件。在插件安装完成后，就可以使用`nvim`打开一个对应的项目（目录）进行编程开发了（如下是一个`C++`项目），这样配置的环境和`vscode`开发环境很像，但内存占用和响应速度会比`vscode`好很多，配置好后就可以愉快的进行开发了。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1202325/1655955559696-f9d11869-ad7a-4ae6-8124-c0e4b3181802.png#clientId=u46c9451e-7bad-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=941&id=u4ac1405a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=941&originWidth=1896&originalType=binary&ratio=1&rotation=0&showTitle=false&size=142267&status=done&style=none&taskId=ud1488b7e-e5f4-4f64-9724-7880b3e7d20&title=&width=1896)

<a name="m1KZ9"></a>
## 语言服务器协议(lsp)配置
当然这个是针对第一种配置方案的，打开对应语言的文件，在命令行模式下输入`:LspInstall`会弹出推荐的语言服务, 我们选择合适的安装，如下是我安装的对应语言的`lsp`：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1202325/1655956119309-c5de8f4d-833a-4198-a316-7e5bb832519f.png#clientId=u46c9451e-7bad-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=858&id=uf2d19a18&margin=%5Bobject%20Object%5D&name=image.png&originHeight=858&originWidth=1897&originalType=binary&ratio=1&rotation=0&showTitle=false&size=173162&status=done&style=none&taskId=uc199c0aa-076f-4e45-adef-9c5a161f954&title=&width=1897)

<a name="ubmyk"></a>
## 快捷键配置
要提高开发效率（装逼能力），配置常用的快捷键是必不可少的。快捷键的配置放在`~/.config/nvim/lua/keymap`下的`lua`脚本中, 我们可以根据对应的描述输入对应的键去熟悉快捷键的使用。另外我加入了如下常用的快捷键的映射：

- 分屏和分屏尺寸控制
> --Split
>  ["n|sv"] = map_cmd(":vsp<CR>"),
>  ["n|sh"] = map_cmd(":sp<CR>"),
>  ["n|sc"] = map_cmd("<C-w>c"),
>  ["n|so"] = map_cmd("<C-w>o"),
> 
> --Split Proportional control
>  ["n|s>"] = map_cmd(":vertical resize +20<CR>"),
>  ["n|s<"] = map_cmd(":vertical resize -20<CR>"),
>  ["n|s="] = map_cmd("<C-w>="),
>  ["n|sj"] = map_cmd(":resize +10<CR>"),
>  ["n|sk"] = map_cmd(":resize -10<CR>"),

- 代码关键字搜索
>  ["n|<Leader>fg"] = map_cu("Telescope live_grep"):with_noremap():with_silent(),

以上配置好后，就可以开始你的编程（装逼）之旅了！


