# 生成密钥

## ED25519 SSH keys

更加安全的新密钥

```bash
ssh-keygen -t ed25519 -C "<comment>"
```

` -C ` 后面可以跟email邮箱作为label

```text
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/user/.ssh/id_ed25519):
```

## RSA SSH keys

使用2048bits
```bash
ssh-keygen -t rsa -b 2048 -C "email@example.com"
```

`-C`后面可以跟email邮箱
```text
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa):
```

# 添加密钥

首先保证已经创建好了密钥

使用不同的方式打开文件

**macOS:**
```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

**Linux (requires the xclip package):**
```bash
xclip -sel clip < ~/.ssh/id_ed25519.pub
```

**Git Bash on Windows:**
```bash
cat ~/.ssh/id_ed25519.pub | clip
```

复制添加到 gitlab

# SSL certificate problem: self signed certificate

问题原因：没有正确的SSL证书

解决方法：

1、定义全局git repo
```bash
export GIT_SSL_NO_VERIFY=true
```

2、关闭SSL检查
```bash
git config --global http.sslVerify false
```
