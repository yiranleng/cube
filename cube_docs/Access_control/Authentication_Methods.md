# Authentication & Identity (认证与身份识别)

## 1. Authentication Methods (认证方式)
- **JSON Web Token (JWT)**：
  - 默认推荐。必须通过 `CUBEJS_API_SECRET` 进行签名验证。
- **Identity Provider (IdP)**：
  - 支持 Auth0, AWS Cognito 等集成。
- **Enterprise Auth**：
  - 支持 **LDAP**, **Kerberos**, **NTLM**。
  - **约束**：通常需要配置额外的驱动或代理。

## 2. Row & Member Level Security (行/成员级安全)
- **Row-level (行级)**：
  - 逻辑硬编码在 `security_context` 过滤器中。
- **Member-level (成员级)**：
  - 动态控制维度或度量的可见性。
  - **要求**：在模型中通过函数式定义 `visible` 属性。

## 3. Security Context Implementation
- **强制过滤 (Mandatory Filtering)**：
  - 通过 `queryRewrite` 钩子注入无法被前端查询覆盖的过滤条件。
  - **安全准则**：绝不要在前端传入过滤条件来隔离租户，必须在后端基于 JWT 内容强制注入。
