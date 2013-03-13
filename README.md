# Url::Cmdline

TODO: Write a gem description

## 准备环境

### 修改varnish配置文件
```sh
vim /etc/default/varnish
# 1) 将配置中的以下内容注释掉
# -a :6081	默认使用80监听，如果前置还有nginx，则应和nginx配置对应
# -S /etc/varnish/secret 这里的意思是不需要认证

# 2) 管理端监听地址，修改可为内网ip地址例如192.178.177.31
# -T localhost:6082 
```

### 修改varnish的VCL文件：default.vcl
```sh
vim /etc/varnish/default.vcl

#添加以下逻辑，注意修改host和port为varnish的管理端口
backend cmd_server {
        .host = "127.0.0.1";
        .port = "3000";
}

sub vcl_recv {
        if (req.url ~ "/cmd/.*/do$") {
                set req.backend = cmd_server;
                return (pass);
        }
	......
}
```

### 最后从启varnish进程
```sh
service varnish restart

# 查看是否已经正确监听了端口
netstat -lnp | grep varnish
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      16827/varnishd  
tcp        0      0 127.0.0.1:6082          0.0.0.0:*               LISTEN      16826/varnishd  
tcp6       0      0 :::80                   :::*                    LISTEN      16827/varnishd 
```

## 安装

```sh
gem install bundle rake

cd ~
git clone https://github.com/huangqiheng/url-cmdline.git
cd url-cmdline
bundle
```

## 配置运行

```sh
cd config
cp url-cmdline-server.yml.sample url-cmdline-server.yml

vim url-cmdline-server.yml #按需修改里面的服务器地址

cd ..
rake
```

