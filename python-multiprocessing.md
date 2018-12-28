## python mutli-processing

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
核心想法也是，将大的dataframe拆成多个，然后用多个proecss并行处理
```python
def (row, a, b):
	return (len(row) + a) / b

def 
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM3Nzk2MTI3MV19
-->