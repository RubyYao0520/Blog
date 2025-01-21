***简单介绍Python对于英文文本的处理***

相较于中文文本，Python对于英文文本的处理过程更加简单。英文文本中单词之间有空格，可以直接用空格作为分词
的标志。
下面的代码使用了Python中的wordcloud库和pandas库，wordcloud用于生成词云，pandas用于写入数据到csv文件。
统计英文小说《简爱》中出现频率最高的20个单词，绘制成词云图片，并把统计数据写入csv文件中。


#### 读取文本
首先我们打开要读取的文件，使用read函数进行读取，这里会对异常输出对应的信息
```python
# 编写主程序  
try:  
    with open(r"D:\python\pythonProject1\sy5\JaneEyre.txt", 'r') as file:  
        f = file.read()  
        f = getText(f)  
        freqs = wordFreq(f, 20)  
# 如果没找到对应的文件，输出相关信息  
except IOError:  
    print("File not found.")  
else:  
    try:  
        with open(r"D:\YAO\python\pythonProject1\sy5\Jane_Eyre_Freq.txt", 'w') as fileFreq:  
            items = [word + '\t' + str(freq) + '\n' for word, freq in freqs]  
            fileFreq.writelines(items)  
    except IOError:  
        print("文件写入错误")  
        for word, freq in freqs:  
            print("{:<10}{:>}".format(word, freq))  

```

#### 对文本初步处理
```python
# getText函数用于读取文本，并且按照标点符号进行分词
#首先把标点符号替换成空格，方便后面分词  
def getText(text):  
    text = text.lower()  
    for ch in ",.;?-:\'":  
        text = text.replace(ch, " ")  
    return text  
```

#### 统计词频
注意这里exculdes中的虚词是在运行多次以后，根据自己的需要添加的，一开始excludes为空，运行一次程序后可以发现记录词频的txt文件里面排名靠前的词多为无意义的虚词，将这些虚词添加到excludes里面，再运行一次程序即可。
```python
# 编写函数统计单词出现的频率并输出词云文件  
# text为待统计文本，topn表示取频率最高的单词个数  
def wordFreq(text, topn):  
    words = text.split()  
    counts = {}  
    # 删除无意义的虚词 exculudes中存储的就是无意义的词 
    for word in words:  
        counts[word] = counts.get(word, 0) + 1  
    excludes = {'the', 'but', 'and', 'of', 'it', 'a', 'an', 'is', 'was', 'not', 'of', 'i', 'to', 'in', '"', 'as', '"i'  
        , 'on', 'that', 'by', 'for', 'be', 'from', 'at', 'this', 'or', 'no', 'with', 'so'}  
    for word in excludes:  
        del counts[word]  
    items = list(counts.items())  
    items.sort(key=lambda x: x[1], reverse=True)  
    # 设置生成词云图片的格式，背景色为白色，长宽等  
    wcloud = WordCloud(background_color="white", width=1000, max_words=20, height=860, margin=1).fit_words(counts)  
    wcloud.to_file(r"D:\python\pythonProject1\sy5\Jane_Eyre.png")  
    return items[:topn]  
```

#### 导出数据
导出相关数据，这一步可根据实际需求选用
```python
# 读取txt文件  
# txt文件路径  
txt_file_path = 'Jane_Eyre_Freq.txt'  
# 写入的csv文件路径  
csv_file_path = 'Jane_Eyre_Freq.csv'  
  
# 使用pandas读取txt文件  
df = pd.read_csv(txt_file_path, delimiter=' ')  
  
# 将数据写入csv文件  
df.to_csv(csv_file_path, index=False)  
  
# 写入成功后输出提示信息  
print(f"Successfully converted {txt_file_path} to {csv_file_path}.")
```

#### 完整代码如下
```python
from wordcloud import *  
import pandas as pd  
  
  
# 统计简爱中出现频率最高的20个单词  
  
# getText函数用于读取文本，并且按照标点符号进行分词
#首先把标点符号替换成空格，方便后面分词  
def getText(text):  
    text = text.lower()  
    for ch in ",.;?-:\'":  
        text = text.replace(ch, " ")  
    return text  
  
  
# 编写函数统计单词出现的频率并输出词云文件  
# text为待统计文本，topn表示取频率最高的单词个数  
def wordFreq(text, topn):  
    words = text.split()  
    counts = {}  
    # 删除无意义的虚词  
    for word in words:  
        counts[word] = counts.get(word, 0) + 1  
    excludes = {'the', 'but', 'and', 'of', 'it', 'a', 'an', 'is', 'was', 'not', 'of', 'i', 'to', 'in', '"', 'as', '"i'  
        , 'on', 'that', 'by', 'for', 'be', 'from', 'at', 'this', 'or', 'no', 'with', 'so'}  
    for word in excludes:  
        del counts[word]  
    items = list(counts.items())  
    items.sort(key=lambda x: x[1], reverse=True)  
    # 设置生成词云图片的格式，背景色为白色，长宽等  
    wcloud = WordCloud(background_color="white", width=1000, max_words=20, height=860, margin=1).fit_words(counts)  
    wcloud.to_file(r"D:\YAO\python\pythonProject1\sy5\Jane_Eyre.png")  
    return items[:topn]  
  
  
# 编写主程序  
try:  
    with open(r"D:\YAO\python\pythonProject1\sy5\JaneEyre.txt", 'r') as file:  
        f = file.read()  
        f = getText(f)  
        freqs = wordFreq(f, 20)  
# 如果没找到对应的文件，输出相关信息  
except IOError:  
    print("File not found.")  
else:  
    try:  
        with open(r"D:\YAO\python\pythonProject1\sy5\Jane_Eyre_Freq.txt", 'w') as fileFreq:  
            items = [word + '\t' + str(freq) + '\n' for word, freq in freqs]  
            fileFreq.writelines(items)  
    except IOError:  
        print("文件写入错误")  
        for word, freq in freqs:  
            print("{:<10}{:>}".format(word, freq))  
  
# 读取txt文件  
# txt文件路径  
txt_file_path = 'Jane_Eyre_Freq.txt'  
# 写入的csv文件路径  
csv_file_path = 'Jane_Eyre_Freq.csv'  
  
# 使用pandas读取txt文件  
df = pd.read_csv(txt_file_path, delimiter=' ')  
  
# 将数据写入csv文件  
df.to_csv(csv_file_path, index=False)  
  
# 写入成功后输出提示信息  
print(f"Successfully converted {txt_file_path} to {csv_file_path}.")
```

#### 统计数据的txt文档内容

<img src="http://image.slugyao.top/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-07-20%20105146.png" width="400">

### 生成的词云图片

<img src="http://image.slugyao.top/Jane_Eyre.png" width="400">

