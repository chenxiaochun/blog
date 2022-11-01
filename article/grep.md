指定`-R`参数，递归的在`src`目录中查找`foo`字符：

```
grep -R foo src/
```

指定`-i`参数，查找时忽略大小写：

```
grep -R -i foo src/
```

指定`-n`参数，打印出匹配所在行的行数：

```
grep -R -n foo src/
```