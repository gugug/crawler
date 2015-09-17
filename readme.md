# coding:utf-8      #是用来指定文件编码为utf-8的
__author__ = 'gu'
from urllib import urlopen   #导入rullib模板用urlopen函数的形式作接口
import  chardet  #导入chardet模板，编码识别模块。
import re    #导入re模板，通过re模块提供对正则表达式的支持
import urllib  #导入urllib模板，获取url
def crawler():
    for i in range(1,3): #数字1，3代表页数，可以传入不同的值来获得某一页的内容。
        url = "http://roll.news.sina.com.cn/interface/rollnews_ch_out_interface.php?col=90&spec=&type=&ch=01&k=&offset_page=0&offset_num=0&num=100&asc=&page="+str(i)
                    #链接要爬取的网址,把值转换成字符串
        text = urllib.urlopen(url).read()
                #用urllib.urlopen读取网站的源码，创建一个表示远程url的类文件对象，
                # 然后像本地文件一样操作这个类文件对象来获取远程数据。参数url表示远程数据的路径，一般是网址
                # read.（）使用方式与文件对象完全一样，文件中读取数据
    string1='http.*?.shtml'#正则表达式匹配语句--网址，赋予变量string
    pattern1=re.compile(string1)  #返回一个pattern对象#re.compile对规则进行预编译以提速
    link = re.findall(pattern1,text)
                #findall 返回 list,以列表的形式返回text里匹配pattern1的不重叠的子串。
                # string会被从左到右依次扫描，返回的列表也是从左到右一次匹配到的。
                # 如果pattern1里含有组的话，那么会返回匹配到的组的列表；
                # 如果pattern1里有多个组，那么各组会先组成一个元组，然后返回值将是一个元组的列表。
    for i in link:
        print i #输出网址链接
        text0 = urllib.urlopen(i).read()
                #用urllib.urlopen读取网站的源码，创建一个表示远程url的类文件对象，
                # read.（）使用方式与文件对象完全一样，文件中读取数据
        chars=chardet.detect(text0)
                #需要先检测text文件的编码，然后将其转化为另一种编码。 这时候就会用到chardet

        code =chars['encoding']
        text = str(text0).decode(code, 'ignore')
                #decode的作用是将text0文件编码的字符串转换成unicode编码，表示将code编码的字符text转换成unicode编码
        pa = re.compile("<title>(.*?)</title>")#正则表达式匹配语句--title，赋予变量pa
        title = re.findall(pa, text)            #findall 返回 list,以列表的形式返回text里匹配pa的不重叠的子串。

        print title[0]#输出tittle
        file = open("/home/gu/tree test /Python crawler/"+title[0]+".txt","w")#内容输入的路径和文档命名

        string2 = u"<!-- publish_helper name='原始正文' "#匹配正文内容的开始
        pattern2 = re.compile(string2)#返回一个pattern2对象#re.compile对规则进行预编译以提速
        list = re.split(pattern2,text)#第一次分割，text的内容分成两半，string2为分界线
        # print list[1]
        string3 = "<!-- publish_helper_end -->"#第二次分割，匹配正文的结束
        pattern3 = re.compile(string3)
        list = re.split(pattern3,list[1])#list[1]是第一次分割后，正文开始以后的那部分，在list[1]里面再次分割，分界线是正文的结束
        # print list[0]
        pattern4=re.compile("<p>(.*?)</p>")#匹配正文
        pattern5 = re.compile('<.*?>')#匹配无用的信息
        for i in re.findall(pattern4,list[0]):#在两次分割后的内容里面，以列表的形式返回匹配到pattern4的不重叠的子串
            # print i
            j = re.sub(pattern5, '', i)#sub函数把pattern5用空格repl
            print j
            file.write(j.encode("utf-8"))
                    #encode的作用是将unicode编码转换成utf-8编码的字符串，表示将text的unicode转换成utf-8编码的字符串
                    #把转换成utf-8编码的字符写入文档
        file.close()#关闭文件
    return i
print crawler()

