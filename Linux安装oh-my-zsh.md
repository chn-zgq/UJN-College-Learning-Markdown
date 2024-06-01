# Linux下安装并且配置oh-my-zsh

## 1.命令查看shell:

```shell
cat /etc/shells
```

## 2.查看当前shell：

```shell
echo $SHELL
```

## 3.安装zsh:

```shell
sudo apt-get install zsh
```

## 4.默认shell切换为zsh:

```shell
chsh -s /bin/zsh
```

## 5.安装oh-my-zsh:

### 手动安装

```shell
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

### 自动安装

```shell
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 6.修改oh-my-zsh配置文件

```shell
vim ~/.zshrc
```

### 修改主题为ys

```shell
ZSH_THEME="ys"
```

### 添加插件

```shell
plugins=(
  git zsh-autosuggestions extract web-search zsh-syntax-highlighting 
)
```

## 7.插件下载

### 插件高亮语法(zsh-syntax-highlighting)安装:

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### 插件自动补全(zsh-autosuggestions)安装:

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

## 8.加载更新配置文件

```shell
source ~/.zshrc   
```

## 到此结束