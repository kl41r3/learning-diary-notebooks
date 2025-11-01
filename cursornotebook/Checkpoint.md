起因是server2被别的任务占领导致我的程序死了，，

luuvy神说从断点继续！

### operations of cursor

1. `tail -50 /home/algernon-echo/MemoryOS/eval/all_loco_results.json`: 首先查看结果文件末尾，了解一下进度

2. `cd /home/algernon-echo/MemoryOS/eval && python3 -c "import json; data=json.load(open('locomo10.json')); print(f'Total samples: {len(data)}'); print('Sample IDs:'); [print(f'  {i}: {sample[\"sample_id\"]}') for i, sample in enumerate(data[:10])]"`

    这是一个脚本，用于了解原始数据集的信息，比如有多少样本，sample_id；同时抽查前十条数据。 `-c`: command. 直接执行，不要写py文件。

3. `python3 -c "import json; data=json.load(open('all_loco_results.json')); sample_ids = set([item['sample_id'] for item in data]); print(f'Processed samples: {sorted(sample_ids)}'); print(f'Total QA pairs: {len(data)}')"`

    查看结果json文件里的sample_id和已经处理的数据量。for item in data指的是json文件最大的那层。set指集合。

4. `python3 -c "import json; data=json.load(open('locomo10.json')); print('QA pairs per sample:'); [print(f'  {sample[\"sample_id\"]}: {len(sample[\"qa\"])} QA pairs') for sample in data]"`

    查看每个数据集中有多少QA对。

5. 修改程序，先load remaining samples，然后继续处理。


python3 -c "import json; data=json.load(open('all_loco_results.json')); print(len(data))"