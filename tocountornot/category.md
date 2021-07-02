# Category

## 新增二级分类

### URL

`POST https://localhost:443/category`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### RequestBody

`{"division":"1","name":"早餐"}`

### ResponseBody

#### 新增成功

```
{
    "code": 201,
    "message": "created",
    "data": 1
}
```

#### 二级分类名过长

```
{
    "code": 400,
    "message": "name length cannot exceed 10",
    "data": null
}
```

#### 二级分类名为空

```
{
    "code": 400,
    "message": "name cannot be blank",
    "data": null
}
```

#### 未指定一级分类

```
{
    "code": 400,
    "message": "division cannot be null",
    "data": null
}
```

#### 指定的一级分类不存在

```
{
    "code": 404,
    "message": "division not found",
    "data": null
}
```

## 修改二级分类

### URL

`PUT https://localhost:443/category/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### RequestBody

`{"division":"1","name":"午餐"}`

### ResponseBody

#### 修改成功

```
{
    "code": 200,
    "message": "success",
    "data": 3
}
```

#### 二级分类不存在

```
{
    "code": 404,
    "message": "category not found",
    "data": null
}
```

#### 未指定一级分类

```
{
    "code": 400,
    "message": "division cannot be null",
    "data": null
}
```

#### 指定的一级分类不存在

```
{
    "code": 404,
    "message": "division not found",
    "data": null
}
```

## 删除二级分类

### URL

`DELETE https://localhost:443/category/{id}`

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

#### 二级分类不存在

```
{
    "code": 404,
    "message": "category not found",
    "data": null
}
```

## 查找二级分类

### URL

`GET https://localhost:443/category/{id}`

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
        "division": 1,
        "name": "早餐"
    }
}
```

#### 二级分类不存在

```
{
    "code": 404,
    "message": "category not found",
    "data": null
}
```

## 一级分类下的所有二级分类

### URL

`GET https://localhost:443/categories/{division}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### ResponseBody

#### 成功

```
{
    "code": 200,
    "message": "success",
    "data": [
        {
            "id": 1,
            "division": 1,
            "name": "早餐"
        },
        {
            "id": 2,
            "division": 1,
            "name": "晚餐"
        },
        {
            "id": 3,
            "division": 1,
            "name": "午餐"
        }
    ]
}
```

#### 指定的一级分类不存在

```
{
    "code": 404,
    "message": "division not found",
    "data": null
}
```