# Bill

## 新增账单

### URL

`POST https://localhost:443/bill`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### RequestBody

```
{
    "card": "2",
    "member": "1",
    "category": "3",
    "price": "12.00",
    "type": "1",
    "time": "1624857024"
}
```

### ResponseBody

#### 新建成功

```
{
    "code": 201,
    "message": "created",
    "data": 1
}
```

#### 账户为空或不存在

```
{
    "code": 400,
    "message": "card cannot be null",
    "data": null
}
```

{
    "code": 404,
    "message": "card not found",
    "data": null
}

#### 成员为空或不存在

```
{
    "code": 400,
    "message": "member cannot be null",
    "data": null
}
```

```
{
    "code": 404,
    "message": "member not found",
    "data": null
}
```

#### 二级分类为空或不存在

```
{
    "code": 400,
    "message": "category cannot be null",
    "data": null
}
```

```
{
    "code": 404,
    "message": "member not found",
    "data": null
}
```

#### 金额为空或不合法

```
{
    "code": 400,
    "message": "price cannot be null",
    "data": null
}
```

```
{
    "code": 400,
    "message": "price should have no more than 9 integer digits or 2 decimal digits",
    "data": null
}
```

```
{
    "code": 400,
    "message": "price cannot be less than zero",
    "data": null
}
```

#### 类型为空或不合法

```
{
    "code": 400,
    "message": "type cannot be null",
    "data": null
}
```

```
{
    "code": 400,
    "message": "type should be 0(income) 1(expense) or 2(transfer)",
    "data": null
}
```

#### 时间戳为空或不合法

```
{
    "code": 400,
    "message": "time cannot be null",
    "data": null
}
```

```
{
    "code": 400,
    "message": "time cannot be a future time",
    "data": null
}
```

## 修改账单

### URL

`PUT https://localhost:443/bill/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### RequestBody

#### 修改成功

```
{
    "card": "2",
    "member": "1",
    "category": "3",
    "price": "12.00",
    "type": "1",
    "time": "1625130521000"
}
```

#### 账单不存在

```
{
    "code": 404,
    "message": "bill not found",
    "data": null
}
```

## 删除账单

### URL

`DELETE https://localhost:443/bill/{id}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### ResponseBody

#### 删除成功

```
{
    "code": 200,
    "message": "success",
    "data": 1
}
```

#### 账单不存在

```
{
    "code": 404,
    "message": "bill not found",
    "data": null
}
```

## 查找账单

### URL

`GET https://localhost:443/bill/{id}`

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
        "card": 2,
        "member": 1,
        "category": 2,
        "price": 15,
        "type": 1,
        "time": "2021-04-30T09:08:41.000+00:00"
    }
}
```

#### 账单不存在

```
{
    "code": 404,
    "message": "bill not found",
    "data": null
}
```