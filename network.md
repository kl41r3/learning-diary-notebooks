显示timeout。为什么timeout？

0. `ssh -vvv algernon-echo@10.200.14.82`. `-vvv`查看详细日志。
1. `ping 10.200.14.82`. timeout：说明 IP 层就不通（路由、防火墙、VPN、子网隔离等）。
2. `nc -vz 10.200.14.82 22`. 端口探测。还是timeout。
3. `route -n get 10.200.14.82`. 检查流量是从哪里走的。
4. `ifconfig | grep -A 2 utun`. 检查有什么utun（虚拟网卡）。
5. `netstat -rn -f inet | egrep '^(default|10\.200\.14)'`. 检查某个目标 IP 会通过哪张网卡、哪个网关发出去。

commands
- `ping github.com -c 4`. check connectivity of internet.
- `curl -v https://github.com`. 

---
### final solution ⬇️
`sudo route -n add -net 10.200.14.0/24 -interface utun4`
---

kimi：  
原因一句话：  
**10.200.14.82 是 RFC-1918 私网地址，而你本机还正好有一条“更精确”的 10.0.0.0/8 直连（或静态）路由，所以内核按“最长前缀匹配”原则直接走 en0 往家里网关 192.168.2.1 去，根本没进默认路由 utun4。**

把过程拆开看：

1. 路由查表优先级  
   内核选路只看两条规则：  
   - 前缀长度最长（最精确）优先；  
   - 前缀一样长再看 metric／创建顺序。  

2. 你的表里有比“default 0.0.0.0/0”更长的 10.0.0.0/8  
   用 `netstat -rn -f inet` 一定能找到类似  
   ```
   10              link#X        UC             en0
   10.200.14       link#X        UCS            en0
   ```
   这类条目（DHCP 下发时会自动加）。因为 10.200.14.82 落在 10.0.0.0/8 里，匹配长度 8 > 0，于是直接走 en0，**不再向上级默认路由 utun4 转发**。

3. 所以“走了本地”完全正常  
   这不是代理失效，而是“目的地就在私网”或者“家里路由器里另有静态回指”，内核按最短路径送包给网关 192.168.2.1，后续由你家路由器决定是把包继续往内网扔，还是再转发到公司的 VPN/代理。

4. 想验证到底进没进代理  
   测一个公网地址即可：  
   ```
   route -n get 8.8.8.8
   ```
   如果返回  
   ```
   gateway: 198.18.0.1
   interface: utun4
   ```
   就说明默认流量确实被 utun4 接管；  
   而私网 10.x 依然走 en0，是预期行为，不必强行改它。
4. ``

