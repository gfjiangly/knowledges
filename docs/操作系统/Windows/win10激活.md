KMS激活失败可以考虑先修改密码，再KMS激活：

```bash
# 查看系统激活状态
slmgr.vbs -xpr

# 查看系统激活状态详细信息
slmgr.vbs -dlv

# 安装产品密钥（神Key）
slmgr /ipk W269N-WFGWX-YVC9B-4J6C9-T83GX

# 设置KMS服务器
slmgr /skms zh.us.to

# 自动激活
slmgr /ato
```

