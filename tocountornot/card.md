# Card

## 新增账户

### URL

`POST https://localhost:443/card`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### RequestBody

`{"name":"现金"}`

### ResponseBody

#### 新增成功

```
{
    "code": 201,
    "message": "created",
    "data": 1
}
```

#### 账户名过长

```
{
    "code": 400,
    "message": "name length cannot exceed 20",
    "data": null
}
```

#### 账户名为空

```
{
    "code": 400,
    "message": "name cannot be blank",
    "data": null
}
```

## 修改账户

### URL

`PUT https://localhost:443/card/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJ0b20iLCJleHAiOjE2MjUxOTM4MzB9.9hRWXOU8y-5iU3zTJivGD17riiFlwnRT3Yrvf31vlCk`

### RequestBody

`{"name":"零钱"}`

### ResponseBody

#### 修改成功

```
{
    "code": 200,
    "message": "success",
    "data": 1
}
```

#### 账户不存在

```
{
    "code": 404,
    "message": "card not found",
    "data": null
}
```

## 删除账户

### URL

`DELETE https://localhost:443/card/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJ0b20iLCJleHAiOjE2MjUxOTM4MzB9.9hRWXOU8y-5iU3zTJivGD17riiFlwnRT3Yrvf31vlCk`

### ResponseBody

#### 删除成功

```
{
    "code": 200,
    "message": "success",
    "data": 1
}
```

#### 账户不存在

```
{
    "code": 404,
    "message": "card not found",
    "data": null
}
```

## 查找账户

### URL

`GET https://localhost:443/card/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJ0b20iLCJleHAiOjE2MjUxOTM4MzB9.9hRWXOU8y-5iU3zTJivGD17riiFlwnRT3Yrvf31vlCk`

### ResponseBody

#### 查找成功

```
{
    "code": 200,
    "message": "success",
    "data": {
        "id": 2,
        "user": 0,
        "name": "微信"
    }
}
```

#### 账户不存在

```
{
    "code": 404,
    "message": "card not found",
    "data": null
}
```

## 所有账户

### URL

`GET https://localhost:443/cards`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJ0b20iLCJleHAiOjE2MjUxOTM4MzB9.9hRWXOU8y-5iU3zTJivGD17riiFlwnRT3Yrvf31vlCk`

### ResponseBody

```
{
    "code": 200,
    "message": "success",
    "data": [
        {
            "id": 1,
            "user": 0,
            "name": "现金"
        },
        {
            "id": 2,
            "user": 0,
            "name": "微信"
        },
        {
            "id": 3,
            "user": 0,
            "name": "支付宝"
        }
    ]
}
```