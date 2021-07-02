# Division

## 新增一级分类

### URL

`POST https://localhost:443/division`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### RequestBody

`{"name":"饮食"}`

### ResponseBody

#### 新增成功

```
{
    "code": 201,
    "message": "created",
    "data": 1
}
```

#### 一级分类名过长

```
{
    "code": 400,
    "message": "name length cannot exceed 10",
    "data": null
}
```

#### 一级分类名为空

```
{
    "code": 400,
    "message": "name cannot be blank",
    "data": null
}
```

## 修改一级分类

### URL

`PUT https://localhost:443/division/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### RequestBody

`{"name":"日用"}`

### ResponseBody

#### 修改成功

```
{
    "code": 200,
    "message": "success",
    "data": 3
}
```

#### 一级分类不存在

```
{
    "code": 404,
    "message": "division not found",
    "data": null
}
```

## 删除一级分类

### URL

`DELETE https://localhost:443/division/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### ResponseBody

#### 删除成功

```
{
    "code": 200,
    "message": "success",
    "data": 3
}
```

#### 一级分类不存在

```
{
    "code": 404,
    "message": "division not found",
    "data": null
}
```

## 查找一级分类

### URL

`GET https://localhost:443/division/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### ResponseBody

#### 查找成功

```
{
    "code": 200,
    "message": "success",
    "data": {
        "id": 2,
        "user": 0,
        "name": "出行"
    }
}
```

#### 一级分类不存在

```
{
    "code": 404,
    "message": "division not found",
    "data": null
}
```

## 所有一级分类

### URL

`GET https://localhost:443/divisions`

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
            "name": "饮食"
        },
        {
            "id": 2,
            "user": 0,
            "name": "出行"
        }
    ]
}
```