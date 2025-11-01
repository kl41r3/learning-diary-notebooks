`tail -50 /home/algernon-echo/A-mem/logs/eval_ours_gpt-4o-mini_openai_ratio1.0_2025-09-03-21-11.log`  
检查log文件的最后50行

---

`grep -i "processing sample" /home/algernon-echo/A-mem/logs/eval_ours_gpt-4o-mini_openai_ratio1.0_2025-09-04-23-29.log | tail -5`

---

`grep -i "error\|exception\|failed\|traceback" /home/algernon-echo/A-mem/logs/eval_ours_gpt-4o-mini_openai_ratio1.0_2025-09-03-21-11.log | tail -10` 

这条命令的作用是：

1. **过滤日志文件中的错误信息**：
   - `grep -i "error\|exception\|failed\|traceback"`：在日志文件中查找所有包含 "error"、"exception"、"failed" 或 "traceback" 的行，`-i` 表示忽略大小写。

2. **显示最后10条匹配结果**：
   - `tail -10`：只显示过滤结果的最后10行。

**总结**：这条命令会从指定的日志文件中快速提取出最后10条与错误相关的信息（包括异常、失败或追踪信息），方便你快速定位最近出现的问题。

`lsof | grep -E "(python|eval)" | head -10`

`ps aux | grep -E "(python|eval)"` 查看最近进程 ps：process status

`-h`: humam-readable


| 命令            | 全称/用途               | 作用                        | 常见输出列                                       |
| ------------- | ------------------- | ------------------------- | ------------------------------------------- |
| **`df -h`**   | **disk filesystem** | 查看**磁盘分区**的使用情况           | Filesystem、Size、Used、Avail、Use%、Mounted on  |
| **`free -h`** | **free memory**     | 查看\*\*内存（含 swap）\*\*的使用情况 | total、used、free、shared、buff/cache、available |


`mv oldname newname`: backup, or move to a new file

---

`sed -n '/^Aggregate Metrics:/,$p' "$latest_log" | sed '1d'`: sed: stream editor  
输出文件中 Aggregate Metrics 的部分，1d: 1 delete. 删除第一行，即 Aggregate Metrics 本身。

---

`scp`: secure copy. 服务器和本地的连接。






