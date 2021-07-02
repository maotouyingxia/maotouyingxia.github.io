# Member

## 新增成员

### URL

`POST https://localhost:443/member`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### RequestBody

`{"name":"我"}`

### ResponseBody

#### 新增成功

```
{
    "code": 201,
    "message": "created",
    "data": 1
}
```

#### 成员名过长

```
{
    "code": 400,
    "message": "name length cannot exceed 10",
    "data": null
}
```

#### 成员名为空

```
{
    "code": 400,
    "message": "name cannot be blank",
    "data": null
}
```

## 修改成员

### URL

`PUT https://localhost:443/member/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### RequestBody

`{"name":"自己"}`

### ResponseBody

#### 修改成功

```
{
    "code": 200,
    "message": "success",
    "data": 1
}
```

#### 成员不存在

```
{
    "code": 404,
    "message": "member not found",
    "data": null
}
```

## 删除成员

### URL

`DELETE https://localhost:443/member/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### ResponseBody

#### 删除成功

```
{
    "code": 200,
    "message": "success",
    "data": 4
}
```

#### 成员不存在

```
{
    "code": 404,
    "message": "member not found",
    "data": null
}
```

## 查找成员

### URL

`GET https://localhost:443/member/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### ResponseBody

#### 查找成功

```
{
    "code": 200,
    "message": "success",
    "data": {
        "id": 1,
        "user": 0,
        "name": "我"
    }
}
```

#### 成员不存在

```
{
    "code": 404,
    "message": "member not found",
    "data": null
}
```

## 所有成员

### URL

`GET https://localhost:443/members`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### ResponseBody

```
{
    "code": 200,
    "message": "success",
    "data": [
        {
            "id": 1,
            "user": 0,
            "name": "我"
        },
        {
            "id": 2,
            "user": 0,
            "name": "爸爸"
        },
        {
            "id": 3,
            "user": 0,
            "name": "妈妈"
        }
    ]
}
```