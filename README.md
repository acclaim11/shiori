# Shiori

[![Go Report Card](https://goreportcard.com/badge/github.com/go-shiori/shiori)](https://goreportcard.com/report/github.com/go-shiori/shiori)
[![Docker Image](https://img.shields.io/static/v1?label=image&message=Docker&color=1488C6&logo=docker)](https://hub.docker.com/r/radhifadlillah/shiori)
[![Deploy Heroku](https://img.shields.io/static/v1?label=deploy&message=Heroku&color=430098&logo=heroku)](https://heroku.com/deploy)
[![Donate PayPal](https://img.shields.io/static/v1?label=donate&message=PayPal&color=00457C&logo=paypal)](https://www.paypal.me/RadhiFadlillah)
[![Donate Ko-fi](https://img.shields.io/static/v1?label=donate&message=Ko-fi&color=F16061&logo=ko-fi)](https://ko-fi.com/radhifadlillah)

Shiori is a simple bookmarks manager written in Go language. Intended as a simple clone of [Pocket](https://getpocket.com//). You can use it as command line application or as web application. This application is distributed as a single binary, which means it can be installed and used easily.

![Screenshot](https://raw.githubusercontent.com/go-shiori/shiori/master/docs/readme/cover.png)

## Features

- Basic bookmarks management i.e. add, edit, delete and search.
- Import and export bookmarks from and to Netscape Bookmark file.
- Import bookmarks from Pocket.
- Simple and clean command line interface.
- Simple and pretty web interface for those who don't want to use a command line app.
- Portable, thanks to its single binary format.
- Support sqlite3, PostgreSQL and MySQL as its database.
- Where possible, by default `shiori` will parse the readable content and create an offline archive of the webpage.
- [BETA] [web extension](https://github.com/go-shiori/shiori-web-ext) support for Firefox and Chrome.

![Comparison of reader mode and archive mode](https://raw.githubusercontent.com/go-shiori/shiori/master/docs/readme/comparison.png)

## Documentation

All documentation is available in [wiki](https://github.com/RadhiFadlillah/shiori/wiki). If you think there are incomplete or incorrect information, feels free to edit it.

## License

Shiori is distributed using [MIT license](https://choosealicense.com/licenses/mit/), which means you can use and modify it however you want. However, if you make an enhancement for it, if possible, please send a pull request. If you like this project, please consider donating to me either via [PayPal](https://www.paypal.me/RadhiFadlillah) or [Ko-Fi](https://ko-fi.com/radhifadlillah).

##搭建教程

1、安装Docker

#CentOS 6
rpm -iUvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum update -y
yum -y install docker-io
service docker start
chkconfig docker on

#CentOS 7、Debian、Ubuntu
curl -sSL https://get.docker.com/ | sh
systemctl start docker
systemctl enable docker
2、拉取镜像

docker run -d --name shiori --restart=always -p 8080:8080 -v ~/shiori:/srv/shiori radhifadlillah/shiori
安装好了后，访问地址：http://ip:8080，初始管理员用户名和密码为shiori和gopher。

如果CentOS系统打不开的话，可能还需要开启8080端口，使用命令：

#CentOS 6
iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
service iptables save
service iptables restart

#CentOS 7
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload
像阿里云等服务器，还需要去安全组那里开放下端口。

最后你想绑定域名，那么就进行第3步，直接ip访问就不用绑定了。

3、绑定域名
安装Caddy：

wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
#备用地址
wget -N --no-check-certificate https://www.moerats.com/usr/shell/Caddy/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
配置Caddy：

#以下全部内容是一个整体，请修改域名后一起复制到SSH运行！

#http访问，该配置不会自动签发SSL
echo "www.moerats.com {
 gzip
 proxy / 127.0.0.1:8080 {
    header_upstream Host {host}
    header_upstream X-Real-IP {remote}
    header_upstream X-Forwarded-For {remote}
    header_upstream X-Forwarded-Port {server_port}
    header_upstream X-Forwarded-Proto {scheme}
  }
}" > /usr/local/caddy/Caddyfile

#https访问，该配置会自动签发SSL，请提前解析域名到VPS服务器
echo "www.moerats.com {
 gzip
 tls admin@moerats.com
 proxy / 127.0.0.1:8080 {
    header_upstream Host {host}
    header_upstream X-Real-IP {remote}
    header_upstream X-Forwarded-For {remote}
    header_upstream X-Forwarded-Port {server_port}
    header_upstream X-Forwarded-Proto {scheme}
  }
}" > /usr/local/caddy/Caddyfile
tls参数会自动帮你签发ssl证书，如果你要使用自己的ssl，改为tls /root/xx.crt /root/xx.key即可，后面为ssl证书路径。

启动Caddy：

/etc/init.d/caddy start
就可以打开域名进行访问了。

最后由于没有修改管理员默认账号和用户名的地方，可以直接去设置处添加一个用户，那么该用户默认会成为管理员，此前的管理员会被停用。
