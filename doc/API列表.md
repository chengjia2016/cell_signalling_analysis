# API list

## 1 /create_customer_raw_data [ok]
创建客户原始格式数据表  
参数:  
1. fields      
2. project_id    

## 2 /upload_data [ok]
上传客户原始格式数据  
参数:  
1. data_path  
2. threads_num  
3. row_delimiter  
4. col_delimiter  
5. project_id  

## 3 /request_task_progress [ok]
获取各种任务执行进度  
参数:  
1. project_id  
2. task_id  

## 4 /transform_to_inner_format [ok]
将客户格式数据转换到内部时空格式    
同时:  
1. 对数据进行按照日期的分区,  
2. 在(uuid, time, lat, lon)粒度上的去重  
3. 和初级的过滤, 各字段null, 0等异常值去除  
参数:  
1. project_id  

## 5 /compute_raw_data_stat [ok]
计算每天的数据条数, 可知道有几天数据, 每天的数据条数  
参数:  
1. project_id  

## 6 /get_raw_data_stat [ok]
获取每天的数据条数, 可知道有几天数据, 每天的数据条数  
参数:  
1. project_id  
返回结果是以`#`隔开的, 按照分区日期排序, 每天的数据条数  

```
20151229,514042#20151230,148#20151231,695#20160101,111
```

## 7 /compute_people_distribution [ok]
计算人基于数据量的分布  
参数:  
1. project_id  
2. interval_size  
3. date_p  
4. top_n  

## 8 /get_people_distribution [ok]
拉取人基于数据量的分布  
参数:  
1. project_id  
返回的结果是, 每组数据用`#`隔开, 按照区间的顺序递增  

```
0,299536#1,1294#2,121#3,74#4,18#5,18#6,8#7,3#8,2#12,1#13,1#14,2#20,2#6105,1
```

## 9 /compute_base_station_info [ok]
计算基站基本信息  
参数:  
1. project_id   

## 10 /download_base_station_info [ok]
下载基站基本信息到本地  
参数:  
1. project_id  

## 11 /get_base_station_info [ok]
获取基站基本信息的本地路径  
参数:  
1. project_id  

## 12 /filter_data_with_range [ok]
利用人基于数据量的分布, 过滤数据  
参数:  
1. project_id  
2. count_min  
3. count_max  

## 13 /compute_filtered_data_stat [ok]
计算过滤后的每天数据条数  
参数:  
1. project_id  

## 14 /get_filtered_data_stat [ok]
获取过滤后的每天数据条数  
参数:   
1. project_id  

## 15 /compute_base_station_hour_summary [ok]
启动小时粒度基站热力  
参数:  
1. project_id  

## 16 /download_base_station_hour_summary [ok]
下载小时粒度基站热力数据到本地  
参数:  
1. project_id  

## 17 /get_base_station_hour_summary [ok]
获取小时粒度基站热力的本地路径  
参数:  
1. project_id  
返回小时粒度激战数据的本地存放路径  

## 18 /compute_uuid_cell_hours [ok]
启动计算人的时空聚合信息, 会返回阿里云上的对应表  
参数:  
1. project_id  

## 18 /compute_uuid_cell_hours [ok]
启动计算人的时空聚合信息, 会返回阿里云上的对应表  
参数:  
1. project_id  

## 19 /get_uuid_cell_hours [ok]
获取阿里云上的时空表表名
参数:  
1. project_id  
