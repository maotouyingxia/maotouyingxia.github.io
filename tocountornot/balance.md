# Balance

## 按账户分类统计收入

### URL

`GET https://localhost:443/income/cards/{year}/{month}`

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
            "id": 7,
            "name": "平安银行",
            "price": 2000
        },
        {
            "id": 2,
            "name": "微信",
            "price": 100
        }
    ]
}
```

#### 月份不存在

```
{
    "code": 400,
    "message": "Invalid value for MonthOfYear (valid values 1 - 12): 13",
    "data": null
}
```

## 按成员分类统计收入

### URL

`GET https://localhost:443/income/members/{year}/{month}`

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
            "name": "我",
            "price": 2100
        },
        {
            "id": 2,
            "name": "爸爸",
            "price": 1500
        }
    ]
}
```

#### 月份不存在

```
{
    "code": 400,
    "message": "Invalid value for MonthOfYear (valid values 1 - 12): 13",
    "data": null
}
```

## 按一级分类分类统计收入

### URL

`GET https://localhost:443/income/divisions/{year}/{month}`

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
            "id": 6,
            "name": "工资",
            "price": 3600
        },
        {
            "id": 8,
            "name": "股票",
            "price": 1000
        }
    ]
}
```

#### 月份不存在

```
{
    "code": 400,
    "message": "Invalid value for MonthOfYear (valid values 1 - 12): 13",
    "data": null
}
```

## 按二级分类分类统计收入

### URL

`GET https://localhost:443/income/categories/{division}/{year}/{month}`

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
            "id": 9,
            "name": "实习",
            "price": 2000
        },
        {
            "id": 10,
            "name": "兼职",
            "price": 100
        },
        {
            "id": 12,
            "name": "生活费",
            "price": 1500
        }
    ]
}
```

#### 月份不存在

```
{
    "code": 400,
    "message": "Invalid value for MonthOfYear (valid values 1 - 12): 13",
    "data": null
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

## 按账户分类统计支出

### URL

`GET https://localhost:443/expense/cards/{year}/{month}`

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
            "id": 2,
            "name": "微信",
            "price": 42
        },
        {
            "id": 3,
            "name": "支付宝",
            "price": 1150
        }
    ]
}
```

#### 月份不存在

```
{
    "code": 400,
    "message": "Invalid value for MonthOfYear (valid values 1 - 12): 13",
    "data": null
}
```

## 按成员分类统计支出

### URL

`GET https://localhost:443/expense/members/{year}/{month}`

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
            "name": "我",
            "price": 1142
        },
        {
            "id": 2,
            "name": "爸爸",
            "price": 50
        }
    ]
}
```

#### 月份不存在

```
{
    "code": 400,
    "message": "Invalid value for MonthOfYear (valid values 1 - 12): 13",
    "data": null
}
```

## 按一级分类分类统计支出

### URL

`GET https://localhost:443/expense/divisions/{year}/{month}`

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
            "name": "饮食",
            "price": 35
        },
        {
            "id": 2,
            "name": "出行",
            "price": 7
        },
        {
            "id": 4,
            "name": "住房",
            "price": 1100
        },
        {
            "id": 7,
            "name": "购物",
            "price": 50
        }
    ]
}
```

#### 月份不存在

```
{
    "code": 400,
    "message": "Invalid value for MonthOfYear (valid values 1 - 12): 13",
    "data": null
}
```

## 按二级分类分类统计支出

### URL

`GET https://localhost:443/expense/categories/{division}/{year}/{month}`

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
            "id": 7,
            "name": "水电费",
            "price": 100
        },
        {
            "id": 8,
            "name": "房租",
            "price": 1000
        }
    ]
}
```

#### 月份不存在

```
{
    "code": 400,
    "message": "Invalid value for MonthOfYear (valid values 1 - 12): 13",
    "data": null
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