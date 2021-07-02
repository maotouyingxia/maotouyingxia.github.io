# User

## 注册

### URL

`POST https://localhost:443/user/register`

### RequestBody

`{"name":"tom","password":"I am tom"}`

### ResponseBody

#### 注册成功

```
{
    "code": 201,
    "message": "created",
    "data": null
}
```

#### 用户名已占用

```
{
    "code": 409,
    "message": "username already taken",
    "data": null
}
```

#### 用户名过长

```
{
    "code": 400,
    "message": "name length cannot exceed 20",
    "data": null
}
```

#### 用户名或密码为空

```
{
    "code": 400,
    "message": "password cannot be blank",
    "data": null
}
```

## 登录

### URL

`POST https://localhost:443/user/login`

### RequestBody

`{"name":"tom","password":"I am tom"}`

### ResponseHeader

`Set-Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJ0b20iLCJleHAiOjE2MjUxOTM4MzB9.9hRWXOU8y-5iU3zTJivGD17riiFlwnRT3Yrvf31vlCk`

### ResponseBody

#### 登录成功

```
{
    "code": 200,
    "message": "success",
    "data": null
}
```

#### 用户不存在

```
{
    "code": 404,
    "message": "user not exist",
    "data": null
}
```

#### 密码错误

```
{
    "code": 403,
    "message": "wrong password",
    "data": null
}
```

## 修改密码

### URL

`PUT https://localhost:443/user`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJ0b20iLCJleHAiOjE2MjUxOTM4MzB9.9hRWXOU8y-5iU3zTJivGD17riiFlwnRT3Yrvf31vlCk`

### RequestBody

`{"name":"tom","password":"I am not tom"}`

### ResponseBody

#### 修改成功

```
{
    "code": 200,
    "message": "success",
    "data": null
}
```

#### 无token

```
{
    "code": 400,
    "message": "no token found",
    "data": null
}
```

#### 无效token

```
{
    "code": 400,
    "message": "invalid token",
    "data": null
}
```

#### 失效token

```
{
    "code": 400,
    "message": "expired token",
    "data": null
}
```

## 删除用户

### URL

`DELETE https://localhost:443/user`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJ0b20iLCJleHAiOjE2MjUxOTM4MzB9.9hRWXOU8y-5iU3zTJivGD17riiFlwnRT3Yrvf31vlCk`

### ResponseBody

```
{
    "code": 200,
    "message": "success",
    "data": null
}
```