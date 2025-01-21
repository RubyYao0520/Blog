之前介绍了用Python中的WordCloud库处理英文文本，这篇文章介绍用Python处理中文文本。
### 选用的IDE
代码运行选用的IDE是Pycharm，其实Spyder，VScode还有Jupyter我也用过。Pycharm安装第三方库的过程更简单，所以这里使用Pycharm。
### 安装需要的第三方库
在Pycharm里面安装第三方库的方法如下
+ 打开Pycharm，进入首页
+ 找到页面右上角设置标志(下图右边第一个)
<img src="http://image.slugyao.top/python%E6%96%87%E6%9C%AC%E5%A4%84%E7%90%86/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-07-22%20085537.png" width="400">

+ 依次点击设置-项目-Python解释器
+ 点击下图中加号，搜索你想下载的第三方库名称，然后下载
+<img src="http://image.slugyao.top/python%E6%96%87%E6%9C%AC%E5%A4%84%E7%90%86/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-07-22%20090833.png" >

**注意：下载第三方库过程可能会报错，建议多尝试几次**

对中文文本进行处理要用到以下第三方库：
+ codecs
+ wordcloud
+ jieba
+ imageio

#### 读取文本
注意所读取的文本要和源代码文件放在同一路径下(同一个文件夹里面)
```python
# 读取文本  
def read_txt(filepath):  
    file = open(filepath, 'r+', encoding='utf-8')  
    txt = file.read()  
    file.close()  
    return txt  
  
  
# 获取小说文本  
txt = read_txt('三国演义.txt')  
  
counts = {}  # 通过键值对的形式存储词语及其出现的次数  
```

#### 对文本进行分词并统计词频
中文文本的分词比英文文本更加困难，英文文本可以使用空格直接进行分词，而中文文本是在句子中根据词意进行分词
对同样一句话，有多种分词方式，比如：我今天去体育场跑步，这一句话可以分为
['我','今天','去','体育场','跑步']，
['我','今天去','体育场','跑步']，抑或是
['我','今天','去体育场','跑步']，而三种不同的划分方式也使分词后词语的词性发生些许变化。

好在python有丰富的第三方库，其中jieba就是可以用于中文分词。jieba(结巴)，将完整的语句分成单个词语，就好像人在说话时结巴。

**首先使用jieba库中的pseg对文本进行分词并且标记词语的词性**
定义了函数getwordTimes对文本进行分词并且统计词频，注意这里的统计都是针对词性为人名的词语进行的
在前几次运行代码后，我发现记录词频的文件里面出现了明显不是人名的词语，比如“孔明曰”，或者是重复的人，比如“云长”就是关羽，因此需要对代码进行修改，添加了elif后面的语句。

```python
# 得到 分词和出现次数  
def getWordTimes():  
    # 分词，返回词性  
    poss = pseg.cut(txt)  
    for w in poss:  
        if w.flag != 'nr' or len(w.word) < 2:  
            continue  # 当分词长度小于2或该词词性不为nr（人名）时认为该词不为人名  
        elif w.word == '孔明' or w.word == '孔明曰' or w.word == '卧龙先生':  
            real_word = '诸葛亮'  
        elif w.word == '云长' or w.word == '关公曰' or w.word == '关公':  
            real_word = '关羽'  
        elif w.word == '玄德' or w.word == '玄德曰' or w.word == '玄德甚' or w.word == '玄德遂' or w.word == '玄德兵' or w.word == '玄德领' \  
                or w.word == '玄德同' or w.word == '刘豫州' or w.word == '刘玄德' or w.word == '玄德大':  
            real_word = '刘备'  
        elif w.word == '孟德' or w.word == '丞相' or w.word == '曹贼' or w.word == '阿瞒' or w.word == '曹丞相' or w.word == '曹将军':  
            real_word = '曹操'  
        elif w.word == '高祖':  
            real_word = '刘邦'  
        elif w.word == '光武':  
            real_word = '刘秀'  
        elif w.word == '桓帝':  
            real_word = '刘志'  
        elif w.word == '灵帝':  
            real_word = '刘宏'  
        elif w.word == '公瑾':  
            real_word = '周瑜'  
        elif w.word == '伯符':  
            real_word = '孙策'  
        elif w.word == '吕奉先' or w.word == '布乃' or w.word == '布大怒' or w.word == '吕布之':  
            real_word = '吕布'  
        elif w.word == '赵子龙' or w.word == '子龙':  
            real_word = '赵云'  
        elif w.word == '卓大喜' or w.word == '卓大怒':  
            real_word = '董卓'  # 把相同意思的名字归为一个人  
        elif w.word == '魏兵':  
            continue  
        else:  
            real_word = w.word  
        counts[real_word] = counts.get(real_word, 0) + 1  
  
  
getWordTimes()  
items = list(counts.items())  
# 进行降序排列 根据词语出现的次数进行从大到小排序  
items.sort(key=lambda x: x[1], reverse=True)  
```


### 自定义词云图片的轮廓
在上一篇Python处理英文文本的文章中已经提到了生成词云图片的方法。这次来给词云图片加一点花样。
在网络上搜索到了一张三国人物的卡通图像，就以这张图片的人物作为词云的轮廓。首先要对图片进行适当处理，**转换成png格式
(原来是jpg格式)**，使用网上的在线转换器就可以。

<img src="http://image.slugyao.top/python%E6%96%87%E6%9C%AC%E5%A4%84%E7%90%86/111.png" width="300">

```python
# 生成词云  
# 我们指定词云的形状为图片111.png的形状 
# font_path指定生成词云的字体路径，使用默认字体即可，当然也可以使用你下载的其他字体 
def creat_wordcloud():  
    bg_pic = imageio.imread('111.png')  
    wc = wordcloud.WordCloud(font_path=r'C:\Windows\Fonts\simhei.ttf',  
                             background_color='white',  
                             width=1000, height=800,   
                             max_words=20,  
                             mask=bg_pic  # mask参数设置词云形状  
                             )  
    # 从单词和频率创建词云  
    wc.generate_from_frequencies(counts)  
  
    # 保存图片  
    wc.to_file('三国演义词云.png')  
```

### 完整代码如下
将代码中的文本路径进行修改，然后是当修改getwordTimes函数，就能对其他中文文本进行人物出现次数的统计了。
```python
import jieba.posseg as pseg  # 引入词性标注接口  
import codecs  
# 词云  
import wordcloud  
import imageio  
  
# 定义主要人物的个数(用于人物关系图,人物出场次数可视化图)  
mainTop = 20  
  
  
# 读取文本  
def read_txt(filepath):  
    file = open(filepath, 'r+', encoding='utf-8')  
    txt = file.read()  
    file.close()  
    return txt  
  
  
# 获取小说文本  
txt = read_txt('三国演义.txt')  
  
counts = {}  # 通过键值对的形式存储词语及其出现的次数  
  
  
# 得到 分词和出现次数  
def getWordTimes():  
    # 分词，返回词性  
    poss = pseg.cut(txt)  
    for w in poss:  
        if w.flag != 'nr' or len(w.word) < 2:  
            continue  # 当分词长度小于2或该词词性不为nr（人名）时认为该词不为人名  
        elif w.word == '孔明' or w.word == '孔明曰' or w.word == '卧龙先生':  
            real_word = '诸葛亮'  
        elif w.word == '云长' or w.word == '关公曰' or w.word == '关公':  
            real_word = '关羽'  
        elif w.word == '玄德' or w.word == '玄德曰' or w.word == '玄德甚' or w.word == '玄德遂' or w.word == '玄德兵' or w.word == '玄德领' \  
                or w.word == '玄德同' or w.word == '刘豫州' or w.word == '刘玄德' or w.word == '玄德大':  
            real_word = '刘备'  
        elif w.word == '孟德' or w.word == '丞相' or w.word == '曹贼' or w.word == '阿瞒' or w.word == '曹丞相' or w.word == '曹将军':  
            real_word = '曹操'  
        elif w.word == '高祖':  
            real_word = '刘邦'  
        elif w.word == '光武':  
            real_word = '刘秀'  
        elif w.word == '桓帝':  
            real_word = '刘志'  
        elif w.word == '灵帝':  
            real_word = '刘宏'  
        elif w.word == '公瑾':  
            real_word = '周瑜'  
        elif w.word == '伯符':  
            real_word = '孙策'  
        elif w.word == '吕奉先' or w.word == '布乃' or w.word == '布大怒' or w.word == '吕布之':  
            real_word = '吕布'  
        elif w.word == '赵子龙' or w.word == '子龙':  
            real_word = '赵云'  
        elif w.word == '卓大喜' or w.word == '卓大怒':  
            real_word = '董卓'  # 把相同意思的名字归为一个人  
        elif w.word == '魏兵':  
            continue  
        else:  
            real_word = w.word  
        counts[real_word] = counts.get(real_word, 0) + 1  
  
  
getWordTimes()  
items = list(counts.items())  
# 进行降序排列 根据词语出现的次数进行从大到小排序  
items.sort(key=lambda x: x[1], reverse=True)  
  
  
# 导出数据  
# 分词生成人物词频（写入文档）  
def wordFreq(filepath, topn):  
    with codecs.open(filepath, "w", "utf-8") as f:  
        for i in range(topn):  
            word, count = items[i]  
            f.write("{}:{}\n".format(word, count))  
  
  
# 生成词频文件  
wordFreq("三国演义词频_人名.txt", 20)  
  
# 将txt文本里的数据转换为字典形式  
fr = open('三国演义词频_人名.txt', 'r', encoding='utf-8')  
dic = {}  
keys = []  # 用来存储读取的顺序  
for line in fr:  
    # 去空白,并用split()方法返回列表  
    v = line.strip().split(':')  
    # 拼接字典 {'诸葛亮', '1373'}  
    dic[v[0]] = v[1]  
    keys.append(v[0])  
fr.close()  
  
# 　绘图# 人名列表 (用于人物关系图,pyecharts人物出场次数图)  
list_name = list(dic.keys())  # 人名  
list_name_times = list(dic.values())  # 提取字典里的数据作为绘图数据  
  
  
# 生成词云  
# 我们指定词云的形状为图片111.png的形状  
def creat_wordcloud():  
    bg_pic = imageio.imread('111.png')  
    wc = wordcloud.WordCloud(font_path=r'C:\Windows\Fonts\simhei.ttf',  
                             background_color='white',  
                             width=1000, height=800,  
                             # stopwords=excludes,# 设置停用词  
                             max_words=20,  
                             mask=bg_pic  # mask参数设置词云形状  
                             )  
    # 从单词和频率创建词云  
    wc.generate_from_frequencies(counts)  
  
    # 保存图片  
    wc.to_file('三国演义词云.png')  
  
  
  
def main():  
    # 词云图  
    creat_wordcloud()  
    # creat_relationship()  
  
  
if __name__ == '__main__':  
    main()
```

### 运行效果
运行成功截图，出现下方语句后等待几秒，就能在代码同一文件夹下看见生成的图片了
<img src="http://image.slugyao.top/python%E6%96%87%E6%9C%AC%E5%A4%84%E7%90%86/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-07-23%20111523.png" width="300">

***代码运行后生成的词云图片如下***
<img src="http://image.slugyao.top/python%E6%96%87%E6%9C%AC%E5%A4%84%E7%90%86/%E4%B8%89%E5%9B%BD%E6%BC%94%E4%B9%89%E8%AF%8D%E4%BA%91.png" width="300">