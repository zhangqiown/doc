## 日常记录
### 8月2日
#### vs code 
设置vs code的终端字体样式 终端最左边的加号 点击配置终端配置 搜索font 进行配置
或者进行如下配置
```json 
  // 设置自己已经安装的字体
  "terminal.integrated.fontFamily": "JetBrainsMono NFM Medium, monofur, Monaco, 'Courier New', monospace,
  JetBrainsMono NFM Medium",
```

#### oh-my-posh 
设置 theme 在 .zshrc 文件中配置

```json
eval "$(oh-my-posh init zsh --config $(brew --prefix oh-my-posh)/themes/if_tea.omp.json)"
// oh-my-posh init zsh --config 配置主题
// $(brew --prefix oh-my-posh)/themes/ 获取路径
// if_tea.omp.json 配置文件 
```

#### zsh
oh-my-zsh 本身自己就有部分主题
ls ~/oh-my-zsh/themes

```json
  // 配置 zsh 自带的主题
  ZSH_THEME="robbyrussell"
```

#### shell 
查看安装的shell cat  /etc/shells 
当前使用的shell echo $SHELL


