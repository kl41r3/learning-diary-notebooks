
`rsync`: remote sync

默认参数：-> `rsync -avz`
* `-a` 保留权限/软链接等
* `-v` 输出详情
* `-z` 压缩，加快传输  


如果使用ssh需要指定密匙：-> `rsync -avz -e "ssh -i ~/.ssh/id_ed25519"`
* `-e` 指定 ssh 参数，避免密钥错用

`\`: 用于转义。`\`后面必须马上换行，不能有空格或注释，否则 shell 会报错


-----



### ChatGPT-5

明白了，你要把 **server2 (10.201.8.114)** 上的所有东西迁移到 **server3 (10.200.14.82)**。这里我给你几个思路，按从最方便到最“保险”的顺序排下来：

---

### 方案 1：直接 `rsync` 整个家目录（本地能登陆两者）

这种方法最常见，能保持文件权限和软链接。

在你本地电脑上执行：

```bash
rsync -avz -e "ssh -i ~/.ssh/id_ed25519" \
  algernon-echo@10.201.8.114:/home/algernon-echo/ \
  algernon-echo@10.200.14.82:/home/algernon-echo/
```

说明：

* `-a` 保留权限/软链接等
* `-v` 输出详情
* `-z` 压缩，加快传输
* `-e` 指定 ssh 参数，避免密钥错用
* 最后的 `/` 代表拷贝目录内容而不是目录本身

这样会把 server2 的家目录完整同步到 server3 的家目录。
如果只想传某个目录，比如 `/home/algernon-echo/MemoryOS/`，把路径改一下即可。

---

### 方案 2：中转下载再上传（适合大文件但网速快）

如果两台服务器之间网络不通，你只能在本地中转：

1. 本地下载 server2 数据：

   ```bash
   rsync -avz -e "ssh -i ~/.ssh/id_ed25519" algernon-echo@10.201.8.114:/home/algernon-echo/ ~/server2_backup/
   ```
2. 上传到 server3：

   ```bash
   rsync -avz -e "ssh -i ~/.ssh/id_ed25519" ~/server2_backup/ algernon-echo@10.200.14.82:/home/algernon-echo/
   ```

缺点是本地需要足够磁盘空间。

---

### 方案 3：server2 直接推送到 server3（最快，如果能互相ssh）

如果 server2 能直接 ssh 到 server3：

```bash
rsync -avz /home/algernon-echo/ algernon-echo@10.200.14.82:/home/algernon-echo/
```

你需要在 server2 上确认：

```bash
ssh gpu-server-3
```

能连上才行。否则只能用方案 1 或 2。

---

### 建议执行步骤

1. **先小范围测试**：比如只同步一个小目录，确认没问题：

   ```bash
   rsync -avz -e "ssh -i ~/.ssh/id_ed25519" algernon-echo@10.201.8.114:~/testdir/ algernon-echo@10.200.14.82:~/testdir/
   ```
2. **再跑全量迁移**。
3. 如果迁移数据量很大，可以加 `--progress` 查看进度，也可以加 `--partial` 避免中断后重传。



非常好，你遇到的是 **rsync 被中断** 的常见情况。好消息是：rsync 支持断点续传，不用重头再来。

---

### 继续下载的方法

在本地重新执行同样的命令，加上 `--partial --progress` 参数：

```bash
rsync -avz --partial --progress -e "ssh -i ~/.ssh/id_ed25519" \
  algernon-echo@10.201.8.114:/home/algernon-echo/ \
  ~/server2_backup/
```

说明：

* `--partial` 会保留已经下载了一部分的文件，下次继续传输。
* `--progress` 可以实时看到进度。
* rsync 会自动跳过已经完全下载的文件，只下载没完成的。


