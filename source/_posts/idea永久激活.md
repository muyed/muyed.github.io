---
title: idea永久激活
date: 2020-01-10 11:21:08
tags: java idea
---

##### 1.下载激活依赖包

{% asset_link jetbrains-agent.jar jetbrains-agent.jar %}

##### 2.在idea中修改idea. vmoptions

点击 `Heap -> Edit Custom Vm options`，在idea. vmoptions文件中添加 `-javaagent:{userpath}/jetbrains-agent.jar`，修改完成后需要重启idea

{% asset_img vmoptions.png  %}

**注意：** `{userpath}`替换成你下载下来的`jetbrains-agent.jar`的存放路径

{% asset_img agent.png  %}

##### 3.激活

点击`Heap -> Register`

{% asset_img reg.png %}

选择`License server`, 会带出注册地址，直接点击`Discover Server`即可完成永久激活

{% asset_img activate.png %}