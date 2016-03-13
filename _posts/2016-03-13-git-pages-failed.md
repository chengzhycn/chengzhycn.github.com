---
layout: post
title:  "github pages 更新失败"
date:   2016-03-13 16:30:00
categories: jekyll
excerpt:    github pages update failed
---

* content
{:toc}

　　在_post内添加了新文章，但是push到远程仓库后，虽然在仓库内可以看到更新，但是在博客内却无法显示更新。  
　　问题的原因：由于之前在本地git服务器上用的校园邮箱和另外一个用户名，导致了使用此用户名push文章时与github账号关联的用户名邮箱不匹配，github服务器解析失败。在本地仓库内设置：

        git config --local user.name <github.name>
        git config --local user.email <github.email>
        
　　更改完后，重新push文章，博客内即可正确显示。