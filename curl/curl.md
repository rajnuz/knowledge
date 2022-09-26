# cUrl 命令

[toc]

## 关闭certificate校验

当访问https时，当通过 cURL 接收到的服务器 CA 未由已安装 CA 证书存储中的受信任证书之一签名时，这将导致 SSL 报告错误，见[Fix “failed to verify the legitimacy of the server” error with cURL](https://linuxpip.org/curl-failed-to-verify-the-legitimacy-of-the-server/)

避免这种情况可以关闭校验，使用`-k` 或 `-insecure`

```shell
curl -k https://random.com

curl -insecure https://random.com
```