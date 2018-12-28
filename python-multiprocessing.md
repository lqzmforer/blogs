## python mutli-processing
### 写在前面
python multipreocessing库提供了方便的进程池管理，利用它可快速进行任务并行处理。几个重要的函数：
* pool.map()/ pool.apply() 函数只能有一个参数
* pool.startmap() 函数可带多个参数
* pool.apply_async() 异步调用，xuya

### CHUNKSIZE
若需要处理的文件过大，可借助pandas分块读取文件进行处理的能力，减少内存占用。
```python
import pandas as pd
import multiprocessing as mp

LARGE_FILE = "D:\\my_large_file.txt"
CHUNKSIZE = 100000 # processing 100,000 rows at a time

def process_frame(df):
        # process data frame
        return len(df)

if __name__ == '__main__':
        reader = pd.read_table(LARGE_FILE, chunksize=CHUNKSIZE)
        pool = mp.Pool(4) # use 4 processes

        funclist = []
        for df in reader:
                # process each data frame
                f = pool.apply_async(process_frame,[df])
                funclist.append(f)

        result = 0
        for f in funclist:
                result += f.get(timeout=10) # timeout in 10 seconds

        print "There are %d rows of data"%(result)
```

### 对已读入的big dataframe，进行拆分处理
核心想法也是，将大的dataframe拆成多个，然后用多个proecss并行处理。这里假设只能按行循环处理，不能进行向量化，并且处理函数带有多个参数的极端情况。
```python
def process_row(row, a, b):
	return (len(row) + a) / b

def process_small_df(df, a, b):
	results = []
	for _, row in df.iterrows():
		result = process_row(row, a, b)
		results.append(result)
	df['results'] = pd.Series({'results':results})
	return df

def process_big_df_parallel(df, a, b, num_partitions=3):
	df_split = np.array_split(df, num_partitions, axis=0)  
	with Pool(num_partitions) as pool:  
	    results_list = pool.starmap(reorder_src_dest_run,zip(df_split, repeat(overlap), repeat(server_set)))  
return pd.concat(results_list, axis=0, ignore_index=True)
```
**NOTE:  **pool.startmap()可以映射带两个及以上的参数。

若处理函数只包含一个参数，那么可以写一个wrapper函数：

```python
def parallelize_dataframe(df, func):  
    """  
 将pandas dataframe进行拆分，用制定的函数func并行处理  :param df:  :param func:  :return:  
 """  
 df_split = np.array_split(df, num_partitions)  
    pool = Pool(num_cores)  
    df = pd.concat(pool.map(func, df_split))  
    pool.close()  
    pool.join()  
    return df
```

### 对dataframe的多列用同一func进行并行处理
```python
from multiprocessing import Pool, cpu_count

def process_Pandas_data(func, df, num_processes=None):
    ''' Apply a function separately to each column in a dataframe, in parallel.'''
    
    # If num_processes is not specified, default to minimum(#columns, #machine-cores)
    if num_processes==None:
        num_processes = min(df.shape[1], cpu_count())
    
    # 'with' context manager takes care of pool.close() and pool.join() for us
    with Pool(num_processes) as pool:
        
        # we need a sequence of columns to pass pool.map
        seq = [df[col_name] for col_name in df.columns]
        
        # pool.map returns results as a list
        results_list = pool.map(func, seq)
        
        # return list of processed columns, concatenated together as a new dataframe
        return pd.concat(results_list, axis=1)
```



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3NjY2MTAyOTEsLTE5MzcwMDYzMTRdfQ
==
-->