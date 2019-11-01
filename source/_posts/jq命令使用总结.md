---
title: jq命令使用总结
date: 2019-10-29 14:18:01
tags:
- jq
categories: 
- 总结
---

jq官方文档： https://stedolan.github.io/jq/manual/
在线jq测试： https://jqplay.org/s/5BJsU3pyPM
jq 表达式 输入文件
类似
cat 输入文件 | jq 表达式

# 测试文件： example.json

字符串数组jsonStringArray和结构体数组JsonObjectArray

```json
{
    "name":"Google",
    "high":{
        "employees":[
            "a",
            "b"
        ]
    },
    "age":[
        {
            "answer":"1",
            "he":"3"
        },
        {
            "answer":"2",
            "he":"4"
        }
    ]
}
```



# 重要命令

## 添加或修改一个key/value

setpath(["key"];"1")
setpath(["key"];"2")

## 修改JsonObject 多层路径下的值指定字段的值

setpath(["high","employees"];["d"])

## 修改JsonArray 多层路径下的值

setpath(["age",1,"answer"];"9527")

## 直接删除k/v

del(.name)
del(.age)

## 删除字符串数组的某个元素

.high.employees -= ["a"]
或者
del(.high.employees[] | select(index("a")))

## 删除结构体数组的某个指定key的元素

del(.age[] | select(.answer == "1") )
或者
.age-= [{"answer":"1","he":"3"}]

## 字符串数组增加元素

.high.employees += ["d"]

## 结构体数组增加元素

.age+= [{"answer":"5","he":"3"}]
或者，指定索引增加
.age[.age| length] |= .+ {"answer":"3","he":"4"}

## 设置临时变量，并引用

jq --argjson obj ‘{"answer":"3","he":"4"}’ ‘.age += [$obj]’ example.json
如果example.json 是数组要用 .[].age

jq --argjson obj "(<input.json)"′.[].age+=[ (<input.json)" '.[].age += [(<input.json)" ′ .[].age+=[obj]’ example.json

## 根据输入文件内容替换过滤器中的结构，

jq --slurp ‘.[0] * {"high":{"employees":.[1]}}’ test.json nodes.json
解释： --slurp / -s read (slurp) all inputs into an array; apply filter to it;
先读取所有的输入放进一个数组，应用过滤器
''内部为过滤器， 第一个0下标，为输入的第0个文件，* 号为后面结构的模糊匹配，如果多个符合就都
选出来， 后面的.[1] 意思是替换指定的数组内容为输入里面的第1个文件内容