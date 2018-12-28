## python mutli-processing

### CHUNKSIZE
若需要处理的文件过大，可借助pandas分块读取文件进行处理的能力，减少内存占用。
```python
import pandas as pd

LARGE_FILE = "D:\\my_large_file.txt"
CHUNKSIZE = 100000 # processing 100,000 rows at a time

def process_frame(df):
        # process data frame
        return len(df)

if __name__ == '__main__':
        reader = pd.read_table(LARGE_FILE, chunksize=CHUNKSIZE)

        result = 0
        for df in reader:
                # process each data frame
                result += process_frame(df)

        print "There are %d rows of data"%(result)
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1OTc1MzcyNzZdfQ==
-->