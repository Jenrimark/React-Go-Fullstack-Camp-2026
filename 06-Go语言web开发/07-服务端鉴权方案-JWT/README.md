# 服务端鉴权方案（JWT）

来源：
- https://campus.wps.cn/contentpreview/f37201f3-39db-4d3c-8c3d-fb31e7f87222

# 服务端鉴权认证方案

# 什么是鉴权

鉴权，即验证用户身份的过程。在互联网应用中，鉴权是确保只有合法用户才能访问特定资源的重要环节。鉴权方案的设计直接关系到系统的安全性和用户体验。

# 基于Token的鉴权

基于Token的鉴权方案是最常见的鉴权方式。用户在登录后，服务器会生成一个Token，并将其返回给客户端。客户端在后续的请求中，需要将Token发送给服务器，服务器会根据Token验证用户身份。

## JWT (JSON Web Token) 原理

JWT是一种开放标准（RFC 7519），它定义了一种紧凑且自包含的方式，用于在各方之间以JSON对象的形式安全地传输信息。

### JWT的结构

JWT由三部分组成，每部分之间用点（.）分隔：

1. Header（头部）：包含token类型和使用的算法
2. Payload（负载）：包含声明（claims）信息，比如用户ID、用户名、过期时间等，Payload是JSON对象，可以包含任何数据，但是不要存储敏感信息，因为Payload是明文传输的
3. Signature（签名）：用于验证token的有效性，使用Header中的算法对Header和Payload进行签名以确保token的完整性并且不可篡改

### JWT认证流程

## JWT的优缺点

### 优点

1. 无状态：JWT本身不存储用户信息，所有信息都存储在Payload中，因此JWT是无状态的，可以轻松实现横向扩展
2. 易于使用：JWT的结构简单，易于使用，并且可以轻松实现跨域认证
3. 易于扩展：JWT的Payload可以包含任何数据，因此可以轻松实现扩展
4. 支持跨域认证：JWT的结构简单，易于使用，并且可以轻松实现跨域认证

### 缺点

1. 无法撤销：JWT一旦生成，无法撤销。退出登录后，之前的JWT仍然有效可能被其他设备使用。
2. 无法存储敏感信息：JWT的Payload可以包含任何数据，但是不要存储敏感信息，因为Payload是明文传输的。

# 基于Session的鉴权

基于Session的鉴权方案是另一种常见的鉴权方式。服务器会为每个登录用户创建一个会话（Session），并将会话ID存储在客户端的Cookie中。

### Session认证流程

# 基于OAuth的鉴权

OAuth是一个开放标准的授权协议，允许用户授权第三方应用访问他们在某个服务提供商上的资源，而无需将用户名和密码提供给第三方应用。

### OAuth 2.0认证流程

## OAuth的优缺点

### 优点

1. 安全性：OAuth使用HTTPS协议，可以确保数据传输的安全性
2. 灵活性：OAuth可以支持多种认证方式，比如密码认证、授权码认证、客户端认证等，可以灵活选择认证方式

### 缺点

1. 复杂性：OAuth的实现相对复杂，需要处理多个步骤和状态管理
2. 用户体验：用户需要跳转到第三方平台进行授权，可能会影响用户体验
3. 依赖第三方：如果第三方服务出现问题，可能会影响自己的服务
4. 维护成本：需要定期更新和维护OAuth配置，包括密钥、回调地址等
5. 安全风险：如果实现不当，可能会存在安全漏洞，如CSRF攻击、重定向攻击等

# 基于API Key的鉴权

API Key是一种简单的鉴权方式，通常用于服务器间的通信或开发者访问API。

### API Key认证流程

# 登录模式

## 单点登录（SSO）

单点登录（Single Sign-On，SSO）是一种身份验证机制，允许用户使用一组凭证访问多个相关但独立的系统。用户只需登录一次，就可以访问所有已授权的系统，无需重复登录。

### SSO工作原理

### SSO的优势

1. 提升用户体验：用户只需登录一次即可访问所有系统
2. 降低管理成本：集中化的用户管理和认证
3. 提高安全性：统一的安全策略和审计
4. 减少密码疲劳：用户只需记住一组凭证

## 多点登录

多点登录（Multiple Login）允许用户在多个设备或浏览器上同时保持登录状态。

### 多点登录实现方案

### 多点登录的实现策略

1. **Token列表方式**

```
type UserToken struct {
    UserID    string
    Token     string
    DeviceInfo string
    LastUsed   time.Time
}

// 存储用户的所有有效Token
type TokenStore struct {
    Tokens map[string][]UserToken
}

// 添加新Token
func (ts *TokenStore) AddToken(userID, token, deviceInfo string) {
    newToken := UserToken{
        UserID:    userID,
        Token:     token,
        DeviceInfo: deviceInfo,
        LastUsed:   time.Now(),
    }
    ts.Tokens[userID] = append(ts.Tokens[userID], newToken)
}

// 验证Token
func (ts *TokenStore) ValidateToken(token string) bool {
    for _, tokens := range ts.Tokens {
        for _, t := range tokens {
            if t.Token == token {
                return true
            }
        }
    }
    return false
}
```

2. **设备标识方式**

```
type DeviceSession struct {
    UserID     string
    DeviceID   string
    Token      string
    LastActive time.Time
}

// 创建或更新设备会话
func CreateDeviceSession(userID, deviceID string) *DeviceSession {
    session := &DeviceSession{
        UserID:     userID,
        DeviceID:   deviceID,
        Token:      generateToken(),
        LastActive: time.Now(),
    }
    return session
}
```

## 单点登录vs多点登录对比

| 特性 | 单点登录（SSO） | 多点登录 |
| --- | --- | --- |
| 登录范围 | 多个系统间共享登录状态 | 同一系统多个设备登录 |
| 用户体验 | 一次登录，访问所有系统 | 每个设备独立登录 |
| 安全性 | 统一的安全策略 | 需要每个设备单独管理 |
| 实现复杂度 | 较高，需要统一认证中心 | 相对简单 |
| 会话管理 | 集中式管理 | 分散管理 |
| 适用场景 | 企业内多系统集成 | 消费级应用 |

## 选择建议

1. **使用单点登录（SSO）的场景**：

   - 企业内部多个系统需要统一认证
   - 需要严格的访问控制和审计
   - 用户经常需要在多个系统间切换
2. **使用多点登录的场景**：

   - 消费级应用（如社交媒体、即时通讯）
   - 用户需要在多个设备上同时使用
   - 对用户体验要求较高的场景

# 最佳实践

1. **使用HTTPS**：无论选择哪种鉴权方案，都应该使用HTTPS保护传输层安全。
2. **Token安全**：

   - 设置合理的过期时间
   - 使用安全的加密算法
   - 定期轮换密钥
3. **错误处理**：

   - 提供清晰的错误信息
   - 限制失败尝试次数
   - 记录异常行为
4. **监控和日志**：

   - 记录鉴权失败的情况
   - 监控异常的访问模式
   - 定期审计访问日志
