# Flow

## 月流水

### URL

`GET https://localhost:443/flow/month/{year}/{month}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### ResponseBody

#### 成功

```
{
    "code": 200,
    "message": "success",
    "data": {
        "income": 2100,
        "expense": 1192,
        "details": [
            {
                "id": 6,
                "card": "支付宝",
                "member": "我",
                "category": "房租",
                "division": "住房",
                "price": 1000,
                "type": 1,
                "time": "2021-04-23T11:14:59.000+00:00"
            },
            {
                "id": 8,
                "card": "微信",
                "member": "我",
                "category": "兼职",
                "division": "工资",
                "price": 100,
                "type": 0,
                "time": "2021-04-24T11:23:59.000+00:00"
            },
            {
                "id": 5,
                "card": "支付宝",
                "member": "我",
                "category": "水电费",
                "division": "住房",
                "price": 100,
                "type": 1,
                "time": "2021-04-25T11:14:59.000+00:00"
            },
            {
                "id": 9,
                "card": "支付宝",
                "member": "爸爸",
                "category": "礼品",
                "division": "购物",
                "price": 50,
                "type": 1,
                "time": "2021-04-26T01:23:59.000+00:00"
            },
            {
                "id": 7,
                "card": "平安银行",
                "member": "我",
                "category": "实习",
                "division": "工资",
                "price": 2000,
                "type": 0,
                "time": "2021-04-27T11:23:59.000+00:00"
            },
            {
                "id": 4,
                "card": "微信",
                "member": "我",
                "category": "公交",
                "division": "出行",
                "price": 2,
                "type": 1,
                "time": "2021-04-28T04:08:41.000+00:00"
            },
            {
                "id": 3,
                "card": "微信",
                "member": "我",
                "category": "地铁",
                "division": "出行",
                "price": 5,
                "type": 1,
                "time": "2021-04-28T09:08:41.000+00:00"
            },
            {
                "id": 11,
                "card": "微信",
                "member": "我",
                "category": "午餐",
                "division": "饮食",
                "price": 13,
                "type": 1,
                "time": "2021-04-29T04:04:42.000+00:00"
            },
            {
                "id": 10,
                "card": "微信",
                "member": "我",
                "category": "早餐",
                "division": "饮食",
                "price": 7,
                "type": 1,
                "time": "2021-04-30T00:04:42.000+00:00"
            },
            {
                "id": 2,
                "card": "微信",
                "member": "我",
                "category": "晚餐",
                "division": "饮食",
                "price": 15,
                "type": 1,
                "time": "2021-04-30T09:08:41.000+00:00"
            }
        ]
    }
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

## 日流水

### URL

`GET https://localhost:443/flow/day/{year}/{month}/{day}`

### RequestHeader

`Cookie: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJqZXJyeSIsImV4cCI6MTYyNTE5NjcwMX0.95usv0Pk0m8gkDUtuhmysBcZQh2jz6x5In1wKQbnwis`

### ResponseBody

#### 成功

```
{
    "code": 200,
    "message": "success",
    "data": {
        "income": 0,
        "expense": 28,
        "details": [
            {
                "id": 13,
                "card": "微信",
                "member": "我",
                "category": "晚餐",
                "division": "饮食",
                "price": 15,
                "type": 1,
                "time": "2021-07-01T02:24:52.000+00:00"
            },
            {
                "id": 14,
                "card": "微信",
                "member": "我",
                "category": "午餐",
                "division": "饮食",
                "price": 13,
                "type": 1,
                "time": "2021-07-01T09:24:52.000+00:00"
            }
        ]
    }
}
```

#### 日期不存在

```
{
    "code": 400,
    "message": "Invalid date 'FEBRUARY 31'",
    "data": null
}
```