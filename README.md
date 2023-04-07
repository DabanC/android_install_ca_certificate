# 安卓12抓HTTPS包
## 安装Magisk
[参考这个](https://sspai.com/post/76276)
## 准备证书
## [参考这个](https://sorayama.me/blog/2020-08-12-pixel3%E7%94%A8charles%E6%8A%93%E5%8C%85%E4%BB%A5%E5%8F%8Aroot/)
#### Charles导出证书
Charles -> Help -> SSL Proxying -> save Charles root certificate
#### 转换证书
```bash
# 获取文件名
openssl x509 -inform PEM -subject_hash_old -in charles-ssl-proxying-certificate.pem | head -1 #aaaaaaaa
openssl x509 -inform PEM -subject_hash -in charles-ssl-proxying-certificate.pem | head -1  #bbbbbbbb

# 分别使用上面的文件名，注意后缀
openssl x509 -inform PEM -text -in charles-ssl-proxying-certificate.pem > aaaaaaaa.0
openssl x509 -inform PEM -text -in charles-ssl-proxying-certificate.pem > bbbbbbbb.0
```
**编辑**输出的文件，把 `-----BEGIN CERTIFICATE-----` 到文·件结束这部分移动到文件首部

#### 制作Magisk模块
模块文件结构为

```bash
charles_ca
├── META-INF
│   └── com
│       └── google
│           └── android
│               ├── update-binary
│               └── updater-script
├── module.prop
└── system
    └── etc
        └── security
            └── cacerts
                ├── aaaaaaaa.0
                └── bbbbbbbb.0
```
将前面转换好的文件移动到 system/etc/security/cacerts 下面，将charles\_ca压缩成zip
#### 安装模块
将压缩包推送到手机

```bash
adb push charles_ca.zip /sdcard/Download/
```
安装本地模块，可以使用这个[FoxMagiskModuleManager](https://github.com/Fox2Code/FoxMagiskModuleManager) 安装

#### 注意⚠️
需要在 SSL Proxying Settings 中配置好需要抓包的url，全部都抓配置成\*:443