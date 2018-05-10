---
layout: post
title: Python for Data Analysis - C02 (1)
description: Python for Data Analysis 第二章 
category: blog
---

**Reference** :

*Python for Data Analysis*

---

[TOC]

### 1.usa.gov from bit.ly

找到两个网站

https://www.data.gov/ 

https://www.usa.gov/developer 

书上的资源在这里可以找到 -> http://1usagov.measuredvoice.com/

跟着书上操作一下看看👀

用的数据是`usagov_bitly_data2012-12-25-1356397867`, 数据的格式为 `json`

#### 读取数据

* **最简单的 python 读法 以及 用 `json` 模块来读**

  `open(path).readline`就是最简单的一种方法，`json.load()`用了` json` 模块

  同时打印出他们的 type 看，发现前者返回 str，后者是一个装 dict 的 list

```python
def test():
    path = 'usagov_bitly_data2012-12-25-1356397867'
    # list comprehension - a concise way of applying an operation
    # to a collection of strings or other objects
    records = [json.loads(line) for line in open(path)]
    return  open(path).readline(), records

line, records = test()

print type(line), line
print type(records), type(records[0]), records[0]
print 'a: ', records[0]['a']
```

```json
<type 'str'> { "a": "Mozilla\/5.0 (iPod; CPU iPhone OS 511 like Mac OS X) AppleWebKit\/534.46 (KHTML, like Gecko) Mobile\/9B206", "c": "US", "nk": 1, "tz": "America\/Indianapolis", "gr": "IN", "g": "Vg7jBV", "h": "Vg7hKn", "l": "iembot", "al": "en-us", "hh": "1.usa.gov", "r": "http:\/\/t.co\/zcHxjnGZ", "u": "http:\/\/www.spc.noaa.gov\/products\/outlook\/archive\/2012\/day1otlk201212250100.html", "t": 1356397870, "hc": 1356397201, "cy": "Washington", "ll": [ 38.665798, -87.175903 ] }

<type 'list'> <type 'dict'> {u'a': u'Mozilla/5.0 (iPod; CPU iPhone OS 511 like Mac OS X) AppleWebKit/534.46 (KHTML, like Gecko) Mobile/9B206', u'c': u'US', u'nk': 1, u'tz': u'America/Indianapolis', u'gr': u'IN', u'g': u'Vg7jBV', u'h': u'Vg7hKn', u'cy': u'Washington', u'l': u'iembot', u'al': u'en-us', u'hh': u'1.usa.gov', u'r': u'http://t.co/zcHxjnGZ', u'u': u'http://www.spc.noaa.gov/products/outlook/archive/2012/day1otlk_20121225_0100.html', u't': 1356397870, u'hc': 1356397201, u'll': [38.665798, -87.175903]}

a:  Mozilla/5.0 (iPod; CPU iPhone OS 511 like Mac OS X) AppleWebKit/534.46 (KHTML, like Gecko) Mobile/9B206
```



#### 用纯 python 计算时区

数据中有一个字段是 `tz` 代表 timezone。假设现在如果要计算哪个时区出现次数最多。

* **用纯 python 来解决计算时区个数**

`counts` 存的是 `tz, count` 键值对

```python
# 最蠢的方法
def get_counts(sequence):
    counts = {}
    for x in sequence:
        if x in counts:
            counts[x] += 1
        else:
            counts[x] = 1
    return counts

# 稍微高级一点点
def get_counts2(sequence):
    counts = collections.defaultdict(int)
    for x in sequence:
        counts[x] += 1
    return counts

# counting time zones in pure python
time_zones = [rec['tz'] for rec in records if 'tz' in rec] # 存了 timezone 的 list
print len(time_zones)                   # 一共有多少条记录包含时区
print get_counts(time_zones).__len__()  # 这些记录里有多少种不同的时区
counts = get_counts(time_zones)
print counts['America/New_York']		# 有多少条记录的时区是 America/New_York
```

结果会是

```python
2209
79
980
```

这里的 `defaultdict`，解释如下图。因为如果没这个元素就自动叫 `int()` 来置0，所以不用像第一种方法那样先判断 key 是否已经存在。 ![c2-defaultdict](../../images/postImages/python4data/c2-defaultdict.png)



* **用纯 python 计算出现次数最多的 10 个时区**

`value_key_pairs` 是一个 list，里面装的是 tuple，默认用第一个元素排序。

```python
def top_counts(count_dict, n=10):
    value_key_pairs = [(count, tz) for tz, count in count_dict.items()]
    value_key_pairs.sort(reverse=True)
    return value_key_pairs

print top_counts(counts)
```

结果会是

```python
[(980, u'America/New_York'), (247, u''), (239, u'America/Chicago'), (205, u'America/Los_Angeles'), (73, u'Asia/Tokyo'), (57, u'Asia/Hong_Kong'), (47, u'Asia/Taipei'), (45, u'Europe/London'), (38, u'America/Denver'), (25, u'America/Indianapolis')]
```



另一个更高级的方法使用 python 的 `Collection.Counter`

```python
counts = collections.Counter(time_zones)
print counts.most_common(10)
```

会得到和上个方法一样的结果 

```python
[(u'America/New_York', 980), (u'', 247), (u'America/Chicago', 239), (u'America/Los_Angeles', 205), (u'Asia/Tokyo', 73), (u'Asia/Hong_Kong', 57), (u'Asia/Taipei', 47), (u'Europe/London', 45), (u'America/Denver', 38), (u'America/Indianapolis', 25)]
```



#### 用 Pandas 计算时区   🚩

Pandas 的核心数据结构是 `DataFrame`, 可以认为是data 的 table 或者是 spreadsheet。

现在先以刚在的 records list 为基础，创建 DataFrame

```python
# Counting Time Zones with pandas
frame = pd.DataFrame(records)
print frame
print frame['tz'][:10]
```

结果是

```python
       gr        h            hc           hh        kw                l  \
0      IN   Vg7hKn  1.356397e+09    1.usa.gov       NaN           iembot   
1      MO   SsNjaC  1.352997e+09    1.usa.gov       NaN      o_d63rn9enb   
2      11   YFfXN8  1.356361e+09    1.usa.gov       NaN            bitly   
3      CA   UX2GJy  1.356137e+09  go.nasa.gov       NaN      nasatwitter   
4      Q9   VraFh0  1.356368e+09    1.usa.gov       NaN    cassinisaturn   
5      48   SMrlFn  1.355848e+09    1.usa.gov       NaN      obriennolan   
6      11   YFfXN8  1.356361e+09    1.usa.gov       NaN            bitly   
7      AZ   qePIGP  1.311670e+09    1.usa.gov       NaN            bitly   
8     NaN   RYPlzL  1.351190e+09  go.nasa.gov       NaN      nasatwitter   
9      08   U799Sf  1.356394e+09    1.usa.gov       NaN      o_9ptkdgt9d   
10    NaN      NaN           NaN          NaN       NaN              NaN   
11     NJ   V6Vw5O  1.356374e+09    1.usa.gov       NaN      twitterfeed   
12     CA   9ndvRw  1.273579e+09       bit.ly       NaN           gohsep   
13    NaN   Th1IdU  1.356381e+09    1.usa.gov       NaN            bitly   
14     CA   UX2GJy  1.356137e+09  go.nasa.gov       NaN      nasatwitter   
15     40   UX2GJy  1.356137e+09  go.nasa.gov       NaN      nasatwitter   
16     IN   Vg7hKn  1.356397e+09    1.usa.gov       NaN           iembot   
17     NY   TS5LMA  1.356372e+09    drudge.tw       NaN        drdg34568   
18    NaN   UX2GJy  1.356137e+09  go.nasa.gov       NaN      nasatwitter   
19    NaN   UX2GJy  1.356137e+09  go.nasa.gov       NaN      nasatwitter   

# frame['tz'][:10]
0    America/Indianapolis
1         America/Chicago
2        Europe/Amsterdam
3     America/Los_Angeles
4           Europe/London
5           Europe/Moscow
6              Asia/Seoul
7         America/Phoenix
8                        
9          Australia/West
Name: tz, dtype: object
```

【frame 的类型是 DataFrame。frame['tz'] 和 tz_counts 都是 Series 类型】

frame['tz']` 有一个` value_counts` 可以实现对于时区的统计。

```python
tz_counts = frame['tz'].value_counts()
print tz_counts[:10]
```

结果如下，可以看到 `value_counts` 直接完成了对于时区的<u>统计以及排序</u>。

```python
America/New_York                  980
                                  247
America/Chicago                   239
America/Los_Angeles               205
Asia/Tokyo                         73
Asia/Hong_Kong                     57
Asia/Taipei                        47
Europe/London                      45
America/Denver                     38
America/Indianapolis               25
```

不过有看到上面 247 那一项对应的时区是空的。是因为原数据有 247 条就没这一项。现在希望把 NA 的标识为 Missing，空字符串的标为 Unknown

```python
clean_tz = frame['tz'].fillna('Missing')
clean_tz[clean_tz == ''] = 'Unknown'
tz_counts = clean_tz.value_counts()
print clean_tz[:10]
```

结果为

```python
America/New_York       980
Unknown                247
America/Chicago        239
America/Los_Angeles    205
Missing                120
Asia/Tokyo              73
Asia/Hong_Kong          57
Asia/Taipei             47
Europe/London           45
America/Denver          38
```

蓝后来画图了🐒

```python
tz_counts[:10].plot(kind='barh', rot=0)
plt.show()
```

 ![c2_figure_1](../../images/postImages/python4data/c2_figure_1.png)

下一个问题： 如何切割 String，比如说只要第一个 token

```python
# 不切割
raw = pd.Series([x for x in frame.a.dropna()])
# 只要第一个 token
results = pd.Series([x.split()[0] for x in frame.a.dropna()])
print raw[:5], results[:5]
```

对比下结果

```python
0    Mozilla/5.0 (iPod; CPU iPhone OS 5_1_1 like Ma...
1    Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKi...
2    Mozilla/5.0 (iPhone; CPU iPhone OS 6_0_1 like ...
3               GoogleProducer; (+http://goo.gl/7y4SX)
4    Mozilla/5.0 (iPhone; CPU iPhone OS 6_0_2 like ...
dtype: object 0        Mozilla/5.0
1        Mozilla/5.0
2        Mozilla/5.0
3    GoogleProducer;
4        Mozilla/5.0
dtype: object
```

 利用 Pandas 和 NumPy 还能轻易完成更复杂的任务，比如说把某个时区再进而分为用 Windows 和 不用 Windows 的。

```python
# OS 是不是 windows
cframe = frame[frame.a.notnull()] # 先剔除 a 为空的
operating_system = np.where(cframe['a'].str.contains('Windows'),'Windows','Not Windows')
print operating_system[:5]
```

看下结果

```python
['Not Windows' 'Windows' 'Not Windows' 'Not Windows' 'Not Windows']
```

这样系统和时区还没到一起，现在将他们组合起来

❓这里的 `groupby`, `unstack`什么的还没弄懂

```python
# group the data by timezone and operating system
# group the data by its time zone column and this new list of operating systems
by_tz_os = cframe.groupby(['tz',operating_system])
# The group counts, analogous to the value_counts function above, can be computed
# using size. This result is then reshaped into a table with unstack
agg_counts = by_tz_os.size().unstack().fillna(0)
print agg_counts[:10]
```

结果会是

```python
                                Not Windows  Windows
tz                                                  
                                      191.0     56.0
Africa/Cairo                            0.0      1.0
Africa/Ceuta                            1.0      0.0
Africa/Johannesburg                     2.0      0.0
Africa/Lagos                            0.0      5.0
America/Anchorage                       2.0      1.0
America/Anguilla                        1.0      0.0
America/Argentina/Buenos_Aires          0.0      4.0
America/Aruba                           1.0      0.0
America/Asuncion                        0.0      1.0
```

最后还需要针对时区进行排序

```python
# select top overall time zones
indexer = agg_counts.sum(1).argsort()
print indexer[:10]
count_subset = agg_counts.take(indexer)[-10:]
print count_subset
```

结果是

```python
tz
                                  39
Africa/Cairo                      29
Africa/Ceuta                      30
Africa/Johannesburg               32
Africa/Lagos                      37
America/Anchorage                 44
America/Anguilla                  45
America/Argentina/Buenos_Aires    51
America/Aruba                     24
America/Asuncion                  58
dtype: int64
                      Not Windows  Windows
tz                                        
America/Indianapolis         17.0      8.0
America/Denver               19.0     19.0
Europe/London                31.0     14.0
Asia/Taipei                   0.0     47.0
Asia/Hong_Kong               46.0     11.0
Asia/Tokyo                   56.0     17.0
America/Los_Angeles         131.0     74.0
America/Chicago             135.0    104.0
                            191.0     56.0
America/New_York            734.0    246.0
```

最后画图🐒

一张是普通的，一张是 normalize 之后

```python
count_subset.plot(kind="barh", stacked=True)
normed_subset = count_subset.div(count_subset.sum(1), axis=0)
normed_subset.plot(kind="barh", stacked=True)
```

 ![c2_figure_2](../../images/postImages/python4data/c2_figure_2.png)

 ![c2_figure_3](../../images/postImages/python4data/c2_figure_3.png)