    使用awk统计IP出现频率：
    awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -nr | head -20
    
    仅统计400错误的IP：
    grep ' 400 ' /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c | sort -nr | head -20


# 分析Nginx日志统计前20个访问IP的命令解析

这是一个经典的Linux命令管道组合，用于从Nginx访问日志中统计出访问量最高的前20个IP地址。下面我将详细解释每个命令的作用：

## 完整命令示例
```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -20
```

## 分步解析

1. **`awk '{print $1}'`**
   - 作用：提取每行的第一个字段
   - 在Nginx日志中，默认空格分隔的第一个字段就是客户端IP地址
   - 示例输入：`192.168.1.1 - - [10/Oct/2023:13:55:36 +0800] "GET / HTTP/1.1" 200 612`
   - 示例输出：`192.168.1.1`

2. **`sort`**
   - 作用：对提取出的IP地址进行排序
   - 目的：让相同的IP地址排列在一起，为后续的`uniq`命令做准备
   - 示例输出：排序后的IP列表（相同IP会连续出现）

3. **`uniq -c`**
   - 作用：统计连续重复行的数量
   - `-c`选项：在每行前显示该行重复出现的次数
   - 示例输出：`25 192.168.1.1`（表示192.168.1.1出现了25次）

4. **`sort -nr`**
   - 作用：对统计结果进行数字排序
   - `-n`：按数值大小排序（而不是字母顺序）
   - `-r`：反向排序（从大到小）
   - 示例输出：按访问次数从高到低排列的IP列表

5. **`head -20`**
   - 作用：只显示前20行结果
   - 最终输出：访问量最高的前20个IP地址及其访问次数

## 实际应用场景

这个命令组合特别适合用于：
- 快速识别高频访问的客户端IP
- 发现可能的恶意扫描或攻击源
- 分析网站流量来源分布
- 监控异常访问行为

## 变体与扩展

1. 如果要分析其他日志字段（如URL）：
```bash
awk '{print $7}' access.log | sort | uniq -c | sort -nr | head -20
```

2. 如果要分析特定时间段的日志：
```bash
grep "10/Oct/2023" access.log | awk '{print $1}' | sort | uniq -c | sort -nr | head -20
```

3. 如果要输出到文件：
```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -20 > top_ips.txt
```

这个命令管道是Linux文本处理能力的经典展示，通过简单的工具组合就能完成复杂的数据统计分析。