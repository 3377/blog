基于 Casdoor 的企业身份统一纳管落地手册。方案以 **Active Directory (AD)** 为底层核心账号源，**Casdoor** 为身份中台，**企业微信** 为移动端门户，最终对接 **致远 OA** 等业务系统。

---

## 1. 核心架构逻辑

- **账号源 (Authority)：** 所有人员增删改在 AD 域中操作。
    
- **同步流 (Identity Sync)：** Casdoor 通过 LDAP 协议定时拉取 AD 变更。
    
- **认证门户 (SSO)：** 致远 OA、企微扫码登录通过 Casdoor 进行协议校验（OIDC/LDAP）。
    

---

## 2. 第一步：对接 AD 域（纳管底层账号）

通过 LDAP 协议将 AD 域用户及其组织架构纳管进 Casdoor。

### 配置路径：`中控台` -> `Providers (提供商)` -> `添加 LDAP`

|**参数名称**|**建议配置**|**说明**|
|---|---|---|
|**Server Name**|`AD-Server`|自定义名称|
|**Server Host**|`192.168.x.x`|域控 IP|
|**Server Port**|`389` 或 `636`|389 为明文，636 为加密(需证书)|
|**Base DN**|`OU=Staff,DC=yourdomain,DC=com`|员工所在的组织单位|
|**Search Filter**|`(&(objectClass=user)(sAMAccountName=%s))`|过滤掉计算机账号，仅查用户|
|**Admin Account**|`CN=Admin,CN=Users,DC=domain,DC=com`|具有读取权限的域账号|
|**Auto Sync**|**开启 (建议 1-5 分钟)**|**实现入离职自动同步的核心**|

### 关键操作：属性映射 (Attribute Mapping)

在 LDAP 配置页面的 Mapping 区域，确保字段对应：

- `uid` ➔ `sAMAccountName` (登录名)
    
- `displayName` ➔ `displayName` (姓名)
    
- `email` ➔ `mail` (邮箱)
    

---

## 3. 第二步：集成企业微信（移动端扫码）

让员工可以使用企业微信扫码登录所有接入 Casdoor 的系统。

### 配置路径：`Providers` -> `添加 WeChat Enterprise`

1. **企微后台获取参数：** 登录企业微信管理后台，获取 `CorpID` 和 `Secret`。
    
2. **设置可信域名：** 在企微后台将 Casdoor 的域名设为可信域名。
    
3. **Casdoor 配置：** 填入 `ClientID (CorpID)` 和 `ClientSecret`。
    
4. **关联逻辑：** 只要企微用户的 UserID 或手机号与 AD 同步过来的账号一致，Casdoor 会自动完成账号绑定。
    

---

## 4. 第三步：对接致远 OA（业务系统接入）

致远 OA 支持标准的 OIDC 协议。

### A. 在 Casdoor 创建应用

1. 进入 `Applications` -> `添加应用`。
    
2. **Redirect URLs:** 填入致远 OA 的回调地址（通常为 `https://oa.xxx.com/seeyon/rest/sso` 类似路径）。
    
3. **获取凭据：** 记下 `Client ID` 和 `Client Secret`。
    

### B. 在致远 OA 配置

1. 登录致远 OA 管理后台 -> 系统设置 -> 单点登录设置。
    
2. 选择 **OIDC 协议**。
    
3. **Issuer URL:** `https://your-casdoor-url.com`
    
4. 填入上述 Client ID 和 Secret。
    
5. **用户名映射：** 选择 `preferred_username` 或 `name`。
    

---

## 5. 账号生命周期（生命周期）落地流程

|**阶段**|**操作动作**|**系统联动逻辑**|
|---|---|---|
|**入职**|HR/IT 在 AD 域创建账号|Casdoor 定时任务扫描到新 DN，自动创建 Casdoor 用户。|
|**权限分配**|在 Casdoor 设置 Group 规则|匹配特定 OU 的用户自动获得致远 OA 访问权限。|
|**日常登录**|员工访问 OA 页面|弹出 Casdoor 中文登录页，员工可输入域账号或用企微扫码。|
|**调岗**|AD 域内移动 OU|Casdoor 下一次同步时自动更新用户的部门属性。|
|**离职/停权**|**AD 侧禁用/删除账号**|Casdoor 同步后将该用户设为 `Suspended`，OA 随即无法登录。|

---

## 6. 避坑与优化建议

1. **完全开源性确认：** 只要您使用的是 Casdoor 的自建 Docker 镜像，上述所有流程涉及的功能均为**免费**。
    
2. **安全加固：** 强烈建议开启 `MFA (多因子认证)`。对于管理员账号，可以强制绑定手机验证码或 TOTP 令牌。
    
3. **同步频率：** 如果公司入离职频繁，建议将 LDAP 同步频率设为 `1min`。Casdoor 的增量同步算法对域控压力很小。
    
4. **日志审计：** 在 Casdoor 的 `Logs` 页面可以查看到所有账号的登录、同步记录，方便满足合规审计需求。