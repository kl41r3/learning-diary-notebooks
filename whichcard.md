`nvidia-smi`: system management interface. 总命令，很好的命令。

`PID`: progress ID.


下次开始跑之前记得检测在用哪张卡！！！！找出问题到底在哪里

这次的解决思路：  
重开，并且在bash和tmux窗口都在export一下两条命令。  
在bash和tmux都用`test.py`验证。  
然后开始跑。跑之后用`nvidia-smi`检查自己到底在用什么。