---
title: 自定义登录异常提示
type: sso
order: 51
description: CAS自定义登录异常提示
keywords: sso,cas,自定义异常
---

在我们系统中用户登录异常的情况可能很多，如密码错误、用户被锁定、用户没有登录权限、以及其他业务异常等等。而为了降低用户理解成本，往往需要根据这些异常场景给用户不同的提示信息。

在jbone单点登录服务中，只需要简单的配置就可以实现自定义异常。
1. 新建自定义异常类
2. 在登录校验逻辑中抛出自定义异常
2. 将自定义异常类配置到登录异常集合中
3. 配置异常提示信息

### 新建自定义异常类
> 根据CAS标准，自定义异常类继承Exeption即可。

首先我们要定义自己的异常类，本例中我们添加两个。

```java

/**
 * 密码错误异常
 */
public class AccountInvalidPasswordException extends RuntimeException {
    public AccountInvalidPasswordException(){super();}
    public AccountInvalidPasswordException(String message){
        super(message);
    }
}

```

```java

/**
 * 用户无权登录
 */
public class AccountPermissionDeniedException extends RuntimeException {
    public AccountPermissionDeniedException(){super();}
    public AccountPermissionDeniedException(String message){
        super(message);
    }
}

```

### 抛出自定义异常

> 本例来自jbone sso的登录校验逻辑

```java

    protected AuthenticationHandlerExecutionResult authenticateUsernamePasswordInternal(UsernamePasswordCredential credential, String originalPassword) throws GeneralSecurityException, PreventedException {
        UserRequestDO userRequestDO = UserRequestDO.buildAll(credential.getUsername(),null);
        Result<UserResponseDO> result = getUserApi().commonRequest(userRequestDO);

        if(result == null || !result.isSuccess() || result.getData() == null){
            logger.warn("用户[{}]不存在",credential.getUsername());
            throw new AccountNotFoundException("用户不存在");
        }

        UserResponseDO userResponseDO = result.getData();


        String caculatePwd = PasswordUtils.getMd5PasswordWithSalt(originalPassword,userResponseDO.getSecurityInfo().getSalt());
        if(!caculatePwd.equals(userResponseDO.getSecurityInfo().getPassword())){
            throw new AccountInvalidPasswordException("密码错误");
        }


        if(userResponseDO.getSecurityInfo().getLocked() == 1){
            logger.warn("用户[{}]已被锁定",credential.getUsername());
            throw new AccountLockedException("用户已被锁定，禁止登录");
        }

        if(userResponseDO.getAuthInfo() == null || CollectionUtils.isEmpty(userResponseDO.getAuthInfo().getRoles()) || !userResponseDO.getAuthInfo().getRoles().contains(requiredRole)){
            logger.warn("用户[{}]没有登录权限",credential.getUsername());
            throw new AccountPermissionDeniedException("用户没有登录权限");
        }
        Map<String,Object> attributes = new HashMap<>();
        attributes.put(SsoConstants.USER_INFO, JSON.toJSONString(converUserInfo(userResponseDO)));

        return createHandlerResult(credential,
                this.principalFactory.createPrincipal(credential.getUsername(),attributes));


    }

```

### 配置登录异常集合

在application.properties中加入以下配置：

```properties

cas.authn.exceptions.exceptions=cn.jbone.sso.common.exceptions.AccountInvalidPasswordException,cn.jbone.sso.common.exceptions.AccountPermissionDeniedException

```

### 配置异常提示信息

在messages_zh_CN.properties中加入以下配置：

> 如果需要支持多语言，还需要在其他语种配置文件中加入。

```properties

# 自定义异常提示
authenticationFailure.AccountInvalidPasswordException=用户名或密码错误。
authenticationFailure.AccountPermissionDeniedException=用户没有登录权限。

```

### 效果

下面是输入错误密码后的提示效果：

![密码错误自定义异常](/images/custom-exception-01.png)
