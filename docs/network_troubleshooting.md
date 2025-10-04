# 网络访问故障排查

在受限的网络环境中，尝试使用 `git fetch` 或其它需要访问 GitHub 的命令时，可能会遇到 `CONNECT tunnel failed, response 403` 的错误。这通常表示当前代理服务阻断了到 GitHub 的 TLS 隧道请求。

## 排查步骤

1. **确认代理是否必要**
   - 查看环境变量 `HTTP_PROXY`、`HTTPS_PROXY` 等。
   - 如果代理并非必需，可暂时通过 `env -u HTTPS_PROXY -u HTTP_PROXY git fetch` 等方式去除代理测试是否恢复连接。

2. **校验证书配置**
   - 确认 `SSL_CERT_FILE` 或 `GIT_SSL_CAINFO` 指向的证书存在且有效。
   - 运行 `git config --global http.sslCAInfo /usr/local/share/ca-certificates/envoy-mitmproxy-ca-cert.crt` 来确保 Git 使用与系统一致的根证书。

3. **联系网络管理员**
   - 如果代理服务器返回 403，说明代理不允许访问目标站点。只能由网络管理员在代理侧放行 GitHub 或提供可用的出口。
   - 记录完整的错误信息（如 `CONNECT tunnel failed, response 403`），以便管理员排查。

4. **考虑离线同步方案**
   - 在无法直接访问远程仓库时，可在外网环境中获取最新代码，使用离线包或镜像源进行同步。

> 注意：由于代理策略和出口限制属于基础设施配置，本仓库无法通过代码更改直接解决该问题，只能提供排查建议。
