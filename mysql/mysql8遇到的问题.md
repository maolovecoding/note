# mysql8遇到的问题

## 学习阶段问题

### 1. 创建函数失败的问题

#### 错误：

[Err] 1418 - This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its declaration and binary logging is enabled (you might want to use the less safe log_bin_trust_function_creators variable)

[Err] 1418-此函数中没有声明DETERMINISTIC，NO SQL或READS SQL DATA，只用声明才会启用二进制日志记录（您可能想使用不太安全的log_bin_trust_function_creators变量）

#### 解决方式：

1. 第一种办法，需要声明**DETERMINISTIC**，**NO SQL**或**READS SQL DATA**的一种
2. 第二种办法，信任子程序的创建者，设置变量**log_bin_trust_function_creators**值为1

```sql
-- 查看该参数，默认为0
select @@log_bin_trust_function_creators;
-- 设置为1
set GLOBAL log_bin_trust_function_creators=1;
```

