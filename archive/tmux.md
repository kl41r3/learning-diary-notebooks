## tmux使用手册
|Command |Function ||
|:------|:------|------|  
|`tmux new -s demo`| 新建会话| `-s` session-name|
|`ctrl+b, d` |断开 |`b` - tmux指令，`d` - detached|
|`tmux attach -t demo` |重连 |`-t` target|
|`exit`|彻底退出会话||
|`ctrl+b, [`| 滚动模式 | `q` 退出|
|`crtl+c`| 终止进程||
|`ctrl+z`| 暂停进程||
|``|||




```bash
# 假装跑个耗时任务
while true; do date; sleep 2; done
```