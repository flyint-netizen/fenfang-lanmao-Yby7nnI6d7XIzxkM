
# RHEL 环境下 Subversion 服务器部署与配置


## 1\. 更新系统


首先确保系统软件包是最新的：



```
sudo yum update -y
```

## 2\. 安装Apache和Subversion


### 2\.1 安装Apache HTTP服务器和Subversion


使用以下命令安装 Apache、Subversion 及其与 Apache 集成的模块：



```
sudo yum install -y httpd subversion mod_dav_svn
```

### 2\.2 启动并设置Apache自启动



```
sudo systemctl start httpdsudo systemctl enable httpd
```

## 3\. 创建SVN仓库


### 3\.1 创建仓库的父目录



```
sudo mkdir -p /var/svn
```

### 3\.2 创建仓库



```
sudo svnadmin create /var/svn/myrepo
```

### 3\.3 设置文件权限



```
sudo chown -R apache:apache /var/svn/myreposudo chmod -R 755 /var/svn/myrepo
```

## 4\. 配置Apache来支持SVN


### 4\.1 编辑Apache配置文件


在 `/etc/httpd/conf.d/` 目录下创建或编辑 `subversion.conf` 文件：



```
sudo vi /etc/httpd/conf.d/subversion.conf
```

添加以下内容：



```
# 加载Subversion模块LoadModule dav_svn_module modules/mod_dav_svn.soLoadModule authz_svn_module modules/mod_authz_svn.so​ /svn>   DAV svn   SVNParentPath /var/svn   AuthType Basic   AuthName "Subversion Repository"   AuthUserFile /etc/svn-auth-users   Require valid-user
```

### 4\.2 创建用户认证文件



```
sudo htpasswd -cm /etc/svn-auth-users user1
```

### 4\.3 配置防火墙



```
sudo firewall-cmd --permanent --add-service=httpsudo firewall-cmd --reload
```

### 4\.4 重启Apache服务



```
sudo systemctl restart httpd
```

## 5\. 测试SVN服务器


### 5\.1 通过Web浏览器测试


访问 `http:///svn/myrepo`。


### 5\.2 使用SVN客户端测试



```
svn checkout http:///svn/myrepo
```

## 6\. 配置SVN访问控制


编辑仓库的 `authz` 文件：



```
sudo vi /var/svn/myrepo/conf/authz
```

示例内容：



```
[groups]developers = user1, user2[/]* = r@developers = rw
```

## 7\. 日志和故障排查


### 7\.1 检查Apache日志



```
sudo tail -f /var/log/httpd/error_log
```

### 7\.2 检查SVN仓库日志



```
sudo tail -f /var/svn/myrepo/logs/repository.log
```

## 8\. 高级配置（可选）


### 8\.1 配置SSL（启用HTTPS）


在 `subversion.conf` 中更改以下内容：



```
   DAV svn   SVNParentPath /var/svn   SSLRequireSSL   AuthType Basic   AuthName "Subversion Repository"   AuthUserFile /etc/svn-auth-users   Require valid-user
```

### 8\.2 使用钩子（Hooks）


SVN支持钩子脚本，可以在仓库的 `hooks` 文件夹中找到相关模板。


### 8\.3 备份和恢复SVN仓库


#### 备份：



```
svnadmin dump /var/svn/myrepo > /backup/myrepo-backup.dump
```

#### 恢复：



```
svnadmin load /var/svn/myrepo < /backup/myrepo-backup.dump
```

  * [RHEL 环境下 Subversion 服务器部署与配置](#tid-DW7j8i)
* [1\. 更新系统](#tid-2XYdFP)
* [2\. 安装Apache和Subversion](#tid-Sdwhpx)
* [2\.1 安装Apache HTTP服务器和Subversion](#tid-6SxeGw)
* [2\.2 启动并设置Apache自启动](#tid-KRREzk)
* [3\. 创建SVN仓库](#tid-7yhGyn)
* [3\.1 创建仓库的父目录](#tid-SsGe42)
* [3\.2 创建仓库](#tid-aJFEia)
* [3\.3 设置文件权限](#tid-NSniGf)
* [4\. 配置Apache来支持SVN](#tid-8FYn8h)
* [4\.1 编辑Apache配置文件](#tid-wMCEHH)
* [4\.2 创建用户认证文件](#tid-zPKjSh)
* [4\.3 配置防火墙](#tid-xr8DAa)
* [4\.4 重启Apache服务](#tid-TRPfBK)
* [5\. 测试SVN服务器](#tid-asNpwi)
* [5\.1 通过Web浏览器测试](#tid-KHb5fi):[milou加速器](https://xinminxuehui.org)
* [5\.2 使用SVN客户端测试](#tid-AwiFre)
* [6\. 配置SVN访问控制](#tid-knydiG)
* [7\. 日志和故障排查](#tid-T2iJNF)
* [7\.1 检查Apache日志](#tid-DzYe5A)
* [7\.2 检查SVN仓库日志](#tid-WQRWXt)
* [8\. 高级配置（可选）](#tid-WDieBF)
* [8\.1 配置SSL（启用HTTPS）](#tid-7zDcT3)
* [8\.2 使用钩子（Hooks）](#tid-bdBCR7)
* [8\.3 备份和恢复SVN仓库](#tid-z6E64d)
* [备份：](#tid-3RPYKB)
* [恢复：](#tid-A7pPjN)

   \_\_EOF\_\_

   ![](https://github.com/fqxm)风轻雪棉  - **本文链接：** [https://github.com/fqxm/p/18474830](https://github.com)
 - **关于博主：** 评论和私信会在第一时间回复。或者[直接私信](https://github.com)我。
 - **版权声明：** 本博客所有文章除特别声明外，均采用 [BY\-NC\-SA](https://github.com "BY-NC-SA") 许可协议。转载请注明出处！
 - **声援博主：** 如果您觉得文章对您有帮助，可以点击文章右下角**【[推荐](javascript:void(0);)】**一下。
     
