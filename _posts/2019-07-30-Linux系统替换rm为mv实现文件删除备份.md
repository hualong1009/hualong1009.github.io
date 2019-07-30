---
layout:     post
title:      修改Linux下rm命令为mv命令
subtitle:   Linux防文件误删必备
date:       2019-07-30
author:     Hansen Wang
header-img: img/post-bg-linux.jpg
catalog: true
tags:
    - Linux
    - Linux安全
---


## Linux OS下替换`rm`为`mv`, 防止文件误删

Linux下command操作，一个迷糊就可能遇到删库跑路的可能，比如杀手`rm -fr *`, 这里提供一种方法替换Linux下的`rm`为`mv`, 实现删除文件备份的功能

## 操作方法

在`/root/.bashrc`中添加如下内容

```shell
function rm_mv(){
	curr_date=$(date +%Y_%m_%d)
	resp=""
	if [ ! -e /tmp/${curr_date} ];then
		mkdir -p /tmp/${curr_date}
	fi
	if echo $1 | grep -q ^'-';then
		echo "Will delete those file : [31m$(echo $@ | cut -d ' ' -f 2-)[0m forcely !"
		resp="y"
	else
		read -p "Will delete those file : [31m$@[0m, right ? [Y|y]|[N|n] > " resp
	fi
	case ${resp} in
		"Y"|"y")
			true
			;;
		*)
			return
			;;
	esac
	
	if echo $1 | grep -q ^'-';then
		file_num=$(echo $@ | cut -d ' ' -f 2- | wc -w)
		flag=0
		for file in $(echo $@ | cut -d ' ' -f 2-)
		do
			if [ -e /tmp/${curr_date}/${file} ];then
				mv -f ${file} /tmp/${curr_date}/${file}_$(date +%s)
				if [ $? -eq 0 ];then
					let flag+=1
				fi
			else
				mv -f ${file} /tmp/${curr_date}
				if [ $? -eq 0 ];then
					let flag+=1
				fi
			fi	
		done
	else
		file_num=$(echo $@ | wc -w)
		flag=0
		for file in $(echo $@)
		do
			if [ -e /tmp/${curr_date}/${file} ];then
				mv -f ${file} /tmp/${curr_date}/${file}_$(date +%s)
				if [ $? -eq 0 ];then
					let flag+=1
				fi
			else
				mv -f ${file} /tmp/${curr_date}
				if [ $? -eq 0 ];then
					let flag+=1
				fi
			fi	
		done
	fi
	if [ ${flag} -eq ${file_num} ];then
		echo "[32mSuccessfully[0m !"
	else
		echo "[31mBUG - Unsuccessfully, [${flag}/${file_num} finished !][0m !"
	fi
}
alias rm='rm_mv'
```



定义一个新的函数`alias`到原来的系统命令`rm`。当执行`rm`文件的时候会提示用户确认删除，删除成功会有返回。同时`rm -fr`还是可以用的，只是不会提示用户确认。遇到删除相同的文件名的时候，会在文件名后+日期备份。每天会按照日期在/tmp下生成备份文件夹。

<font color=#ed1c24>注意，由于markdown无法正确地显示部分字符code中的`●`是CTRL+v+ESC组合键出来的, UTF-8是\<0x1b\></font>

/tmp为备份文件夹：
[![](https://i.loli.net/2019/07/30/5d3fd7e40d52920941.png)](https://i.loli.net/2019/07/30/5d3fd7e40d52920941.png)

删除的时候的操作：
[![](https://upload-images.jianshu.io/upload_images/12855778-469a87e2a7d988a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/460)](https://upload-images.jianshu.io/upload_images/12855778-469a87e2a7d988a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/460)

> 📌转载请注明来源，版权归作者**[@hualong1009](https://hualong1009.github.io)**所有, 谢谢