<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [手机信令分析](#手机信令分析)
	- [数据上传功能](#数据上传功能)
		- [创建测试用表:](#创建测试用表)
		- [确认Tunnel行为, 执行上传tunnel:](#确认tunnel行为-执行上传tunnel)
		- [tunnel upload的参数:](#tunnel-upload的参数)
			- [参数](#参数)
			- [例子](#例子)
				- [多线程实例](#多线程实例)
		- [重构tunnel](#重构tunnel)
			- [当前代码](#当前代码)
	- [命令行上传](#命令行上传)

# 手机信令分析

## 数据上传功能

### 创建测试用表:

```
create table if not exists test_20160828(
  uuid string,
  call_in bigint,
  call_out bigint,
  time bigint,
  cell_id bigint,
  cell_name string,
  lon double,
  lat double,
  in_room bigint,
  is_roam bigint);
```

### 确认Tunnel行为, 执行上传tunnel:

```
odps@ tsnav_project>tunnel upload /Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m test_20160828 -dbr true -rd '\n' -s 'true' -mbr 100000000;
```


对于一个320M的数据, 会被分成4个block, 每个<=100M, tunnel可以先扫描数据是否符合表结构, 然后在上传。可以显示每个block的上传进度, 以及总体的上传进度。

输出:

```
Upload session: 2016082817150343399a0a0078af77
Start upload:data/test/300m
Total bytes:323253738  	 Split input to 4 blocks
2016-08-28 17:15:03    	scan block: '1'
2016-08-28 17:15:05    	scan block complete, blockid=1
2016-08-28 17:15:05    	scan block: '2'
2016-08-28 17:15:07    	scan block complete, blockid=2
2016-08-28 17:15:07    	scan block: '3'
2016-08-28 17:15:09    	scan block complete, blockid=3
2016-08-28 17:15:09    	scan block: '4'
2016-08-28 17:15:09    	scan block complete, blockid=4
2016-08-28 17:15:09    	upload block: '1'
2016-08-28 17:15:14    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	12%    	12.7 MB	2.5 MB/s
2016-08-28 17:15:19    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	25%    	25.7 MB	2.6 MB/s
2016-08-28 17:15:24    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	38%    	38.8 MB	2.6 MB/s
2016-08-28 17:15:29    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	52%    	52 MB  	2.6 MB/s
2016-08-28 17:15:34    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	65%    	65.3 MB	2.6 MB/s
2016-08-28 17:15:39    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	78%    	78.3 MB	2.6 MB/s
2016-08-28 17:15:44    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	91%    	91.4 MB	2.6 MB/s
2016-08-28 17:15:48    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	100%   	100 MB 	2.6 MB/s
2016-08-28 17:15:48    	upload block complete, blockid=1
2016-08-28 17:15:48    	upload block: '2'
2016-08-28 17:15:53    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	12%    	13 MB  	2.6 MB/s
2016-08-28 17:15:58    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	26%    	26.3 MB	2.6 MB/s
2016-08-28 17:16:03    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	39%    	39.6 MB	2.6 MB/s
2016-08-28 17:16:08    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	53%    	53.2 MB	2.7 MB/s
2016-08-28 17:16:13    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	66%    	66.6 MB	2.7 MB/s
2016-08-28 17:16:18    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	79%    	79.9 MB	2.7 MB/s
2016-08-28 17:16:23    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	93%    	93.2 MB	2.7 MB/s
2016-08-28 17:16:26    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	100%   	100 MB 	2.6 MB/s
2016-08-28 17:16:26    	upload block complete, blockid=2
2016-08-28 17:16:26    	upload block: '3'
2016-08-28 17:16:31    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	12%    	12.7 MB	2.5 MB/s
2016-08-28 17:16:36    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	26%    	26 MB  	2.6 MB/s
2016-08-28 17:16:41    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	39%    	39.1 MB	2.6 MB/s
2016-08-28 17:16:46    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	52%    	52.4 MB	2.6 MB/s
2016-08-28 17:16:51    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	65%    	65.7 MB	2.6 MB/s
2016-08-28 17:16:56    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	78%    	79 MB  	2.6 MB/s
2016-08-28 17:17:01    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	92%    	92 MB  	2.6 MB/s
2016-08-28 17:17:04    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	100%   	100 MB 	2.6 MB/s
2016-08-28 17:17:04    	upload block complete, blockid=3
2016-08-28 17:17:04    	upload block: '4'
2016-08-28 17:17:08    	4:314572800:8680938:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m	100%   	8.3 MB 	2.8 MB/s
2016-08-28 17:17:08    	upload block complete, blockid=4
upload complete, average speed is 2.4 MB/s
OK
```

### tunnel upload的参数:

`odps@ tsnav_project>tunnel help upload;`

用法 `tunnel upload [options] <path> <[project.]table[/partition]>`

#### 参数

1. -bs <ARG>: block大小, 单位MB, 默认100
2. -c <ARG>: 设置file的编码格式, **默认不改变编码, 使用raw**
3. -cp <ARG>: 压缩, 默认true
4. -dbr <ARG>: 是否丢弃异常数据, 默认false
5. -dfp <ARG>: 设置日期格式, 默认"yyyy-MM-dd HH:mm:ss"
6. -fd <ARG>: 设置列间隔符, 默认","
7. -h <ARG>: 是否有表头, 默认false
8. -mbr <ARG>: 最多允许多少条异常数据, 默认1000
9. -ni <ARG>: 设置null表示成的字符串, 默认""
10. -rd <ARG>: 设置换行符, 默认"\n"
11. -s <ARG>: 是否先扫描文件再上传, 默认true, only时只扫描
12. -sd <ARG>: 设置session目录, 默认null, **作用?**
13. -te <ARG>: 设置tunnel endpoint
14. -threads <ARG>: 设置线程个数, 默认1
15. -tz <ARG>: 设置时区, 默认本地Asia/Shanghai


#### 例子

把log.txt上传到test_project.test_table的[p1=b1, p2=b2]分区。

`tunnel upload log.txt test_project.test_table/p1="b1",p2="b2"`

##### 多线程实例

还是上传300m, block大小默认100m, 但线程个数是5, 由于block个数是4, 实际线程个数就是4。可以看到CPU利用率提高了(top查看), 每秒上传的数据量也提高了(**从2.4M/s 提高到7M/s**)

```
odps@ tsnav_project>tunnel upload /Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m test_20160828 -dbr true -rd '\n' -s 'true' -mbr 100000000 -threads 5;
Upload session: 201608281737480e399a0a0078d242
Start upload:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m
Total bytes:323253738  	 Split input to 4 blocks
2016-08-28 17:37:48    	scan block: '1'
2016-08-28 17:37:48    	scan block: '2'
2016-08-28 17:37:48    	scan block: '3'
2016-08-28 17:37:48    	scan block: '4'
2016-08-28 17:37:48    	scan block complete, blockid=4
2016-08-28 17:37:50    	scan block complete, blockid=3
2016-08-28 17:37:50    	scan block complete, blockid=2
2016-08-28 17:37:50    	scan block complete, blockid=1
2016-08-28 17:37:50    	upload block: '1'
2016-08-28 17:37:50    	upload block: '2'
2016-08-28 17:37:50    	upload block: '3'
2016-08-28 17:37:50    	upload block: '4'
2016-08-28 17:37:54    	4:314572800:8680938:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m	100%   	8.3 MB 	2.8 MB/s
2016-08-28 17:37:54    	upload block complete, blockid=4
2016-08-28 17:37:55    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	12%    	12.7 MB	2.5 MB/s
2016-08-28 17:37:55    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	11%    	11.8 MB	2.4 MB/s
2016-08-28 17:37:55    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	12%    	12.9 MB	2.6 MB/s
2016-08-28 17:38:00    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	26%    	26 MB  	2.6 MB/s
2016-08-28 17:38:00    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	24%    	24.2 MB	2.4 MB/s
2016-08-28 17:38:00    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	26%    	26.3 MB	2.6 MB/s
2016-08-28 17:38:05    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	39%    	39.1 MB	2.6 MB/s
2016-08-28 17:38:05    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	36%    	36.4 MB	2.4 MB/s
2016-08-28 17:38:05    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	39%    	39.6 MB	2.6 MB/s
2016-08-28 17:38:10    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	48%    	48.9 MB	2.4 MB/s
2016-08-28 17:38:10    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	52%    	52.4 MB	2.6 MB/s
2016-08-28 17:38:10    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	53%    	53.1 MB	2.7 MB/s
2016-08-28 17:38:15    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	61%    	61.3 MB	2.5 MB/s
2016-08-28 17:38:15    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	66%    	66.5 MB	2.7 MB/s
2016-08-28 17:38:15    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	65%    	65.8 MB	2.6 MB/s
2016-08-28 17:38:20    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	73%    	73.7 MB	2.5 MB/s
2016-08-28 17:38:20    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	79%    	79 MB  	2.6 MB/s
2016-08-28 17:38:20    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	79%    	79.7 MB	2.7 MB/s
2016-08-28 17:38:25    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	85%    	85.9 MB	2.5 MB/s
2016-08-28 17:38:25    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	93%    	93.1 MB	2.7 MB/s
2016-08-28 17:38:25    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	92%    	92.1 MB	2.6 MB/s
2016-08-28 17:38:28    	1:0:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	100%   	100 MB 	2.6 MB/s
2016-08-28 17:38:28    	upload block complete, blockid=1
2016-08-28 17:38:28    	3:209715200:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	100%   	100 MB 	2.6 MB/s
2016-08-28 17:38:28    	upload block complete, blockid=3
2016-08-28 17:38:30    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	98%    	98.4 MB	2.5 MB/s
2016-08-28 17:38:31    	2:104857600:104857600:/Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m      	100%   	100 MB 	2.4 MB/s
2016-08-28 17:38:31    	upload block complete, blockid=2
upload complete, average speed is 7 MB/s
OK
```

### 重构tunnel

tunnel的功能完全满足了需求, 目前的问题就是如何把tunnel重构下。以命令行的形式调用tunnel, 而不是进入odps客户端。并且把upload期间的进度信息回传。

#### 当前代码

console入口:

<https://github.com/aliyun/aliyun-odps-console/blob/28dc41559e7c0a4013b2b5e5b8c77294e5509e47/odps-console-basic/src/main/java/com/aliyun/openservices/odps/console/ODPSConsole.java>

陷入:

<https://github.com/aliyun/aliyun-odps-console/blob/28dc41559e7c0a4013b2b5e5b8c77294e5509e47/odps-console-basic/src/main/java/com/aliyun/openservices/odps/console/utils/CommandParserUtils.java>

中间寻找到tunnel命令:

**TODO**

最终位置:

<https://github.com/wang-yang/aliyun-odps-console/blob/master/odps-console-dship/src/main/java/com/aliyun/odps/ship/DShipCommand.java>

## 使用命令行工具

目前可以通过命令行的形式调用上传脚本, 以及sql脚本  


```
./odpscmd -e "tunnel upload /Users/willwywang-NB/github/cell_signalling_analysis/data/test/300m test_20160828 -dbr true -rd '\n' -s 'true' -mbr 100000000 -threads 4;"
```

**现在的重点是把上传，执行sql等的返回通过程序获取到**