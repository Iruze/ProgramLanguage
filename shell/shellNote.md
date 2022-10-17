### []和[[]]的区别
- 判断变量是否为空

`-z`或者`-n`的使用
```shell script
# 正确
[ -z "$b" ]  或者 [[ -z $b ]]

# 错误
[ -z $b ]   // b可能为空
```

- 组合条件的判断
> 在使用"[[  ]]"时，不能使用"-a"或者"-o"对多个条件进行连接;

> 在使用"[[]]"时, "&&"或者"||"既可以放在"[[]]"之内,又可以放在之外,形成多个单独的判断条件;

>在使用"[  ]"时，如果使用"-a"或者"-o"对多个条件进行连接，"-a"或者"-o"必须被包含在"[ ]"之内;

>在使用"[  ]"时，如果使用"&&"或者"||"对多个条件进行连接，"&&"或者"||"必须在"[ ]"之外。


- 运算符转义

1). `>`或者`<`判断字符串的**ASCII**值大小时

`[[]]`会按照大于或者小于运算, `[]`则会按照**重定向**报错;

2). `=~`正则表达式匹配, 只能用于`[[]]`
```shell script
# tel=13949158392
# [[ $tel =~ [0-9]{11} ]]  # 有4个空格
# echo $?
```

### sed
[Linux sed 命令](https://www.runoob.com/linux/linux-comm-sed.html)

sed
- 删除含有关键字的行：
```shell
sed -i '/^export zzw=1/d' test
```
- 匹配到 test 中 zzw = 1 中的值1
```shell
sed -n '/license_modle = vm/p' test | awk -F'=' '{print $2}'
```
- 去掉字符串左右的空格
```shell
t=' ABC'
t1=`echo $t`
echo $t1 (ABC, 去掉了空格)
```

### python中的正则表达式匹配re

[如何用python正则表达式匹配字符串？](https://www.php.cn/python-tutorials-451669.html)

- 获取`vswitch`的名字
```python
import re
re.findall(r'.*:(.*)', 'icicvlan1:VSICIC')

# 输出
# VSICIC
```

贪婪匹配和非贪婪匹配
```python
import re
str = "a123b456b"
print re.findall(r"a(.+?)b", str)

#输出['123']#?控制只匹配0或1个,所以只会输出和最近的b之间的匹配情况

print re.findall(r"a(.+)b", str)
#输出['123b456']

print re.findall(r"a(.*)b", str)
#输出['123b456']
```

忽略大小写:
```python
ret_code = re.findall(r'.*Return Code:\s*(\d*).*', cmdOutput,
                                          flags=re.IGNORECASE)[0]
```

- 获取`icic-services status`中`status`
```python
t = 'abdc\n\\r Active: active (running) \nabc'
# ([^ ]*)匹配':'之后的非空字符串
ans = re.findall(r'Active:[ ]*([^ (]+)', t)
```
