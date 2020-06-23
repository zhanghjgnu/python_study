## python汉字转拼音和获取汉字首字母

python有个汉字转拼音的库pypinyin,不过我的pycharm项目(采用anaconda做环境)安装不了这个包，需要手工安装一下，步骤如下：
打开cmd
```
cd D:\ProgramData\Anaconda3\envs\spiderdxy\Scripts
pip install pypinyin
```
安装后成功


实现代码如下:
```
import pypinyin

# 不带声调的(style=pypinyin.NORMAL)
def pinyinnormal(word):
    s = ''
    for i in pypinyin.pinyin(word, style=pypinyin.NORMAL):
        s += ''.join(i)
    return s

# 带声调的(默认)
def yinjie(word):
    s = ''
    # heteronym=True开启多音字
    for i in pypinyin.pinyin(word, heteronym=True):
        s = s + ''.join(i) + " "
    return s

# 拼音字头
def get_acronym(str_data):
    """
    获取字符串的首字母
    :param str_data: 中文字符串
    :return: 拼音首字母字符串
    """
    return "".join([i[0][0] for i in pypinyin.pinyin(str_data)])


if __name__ == '__main__':
    print("默认拼音",pinyinnormal("苜蓿草科技"))
    print("带音节拼音",yinjie("苜蓿草科技"))
    print("拼音首字母", get_acronym('苜蓿草科技'))
    
```
