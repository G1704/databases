# -*-coding:utf-8-*-
import io
import os
import re
from selenium import webdriver
from time import sleep
from pyquery import PyQuery as pq
from mondoss import *

def creater():
    # 创建文件夹并开始爬去
    dir = '/home/wayne/PycharmProjects/tianmao/venv/'
    for i in range(2014, 2016):
        # dir_name = str(i)
        # os.mkdir(dir_name)
        # print('成功创建文件夹！')
        with open(dir + str(i) + '.txt', "r") as ff:
            info_lines = ff.read().split('\n')
            crawler(info_lines, i) # 调用crawler爬取信息
            ff.close()


def crawler(info_lines, i):
    # 爬取信息并写入
    for info_line in info_lines:
        if (info_line):
            movie_name = info_line.split('\x01')[0]
            movie_id = info_line.split('\x01')[1]
            final_year = ""
            # with open('./%s/%s.txt' % (str(i), movie_name), 'wb') as f:
            url = 'http://search.mtime.com/search/?q=%s' % movie_name
            print(url)
            browser = webdriver.Chrome()
            browser.get(url)
            sleep(0.5)
            content = browser.execute_script("return document.documentElement.outerHTML")
            urls = re.findall(
                '<a pan="M14_SearchIndex_MoreResult_MovieCover" class="pic __r_c_" target="_blank" href="(.*?)">',
                content)
            final_url = ""
            flag = 1
            for url1 in urls:
                browser.get(url1)
                sleep(1)
                content2 = browser.execute_script("return document.documentElement.outerHTML")
                name = re.findall('<h1 style="font-size:35px;" property="v:itemreviewed">(.*?)</h1>', content2)[0]
                if str(name) == str(movie_name):
                    final_url = url1
                    flag = 0
                    break
            # iid = '_id：' + movie_id + '\n'; year = 'year：' + str(i) + '\n'; movie_names = 'movie_name：' + movie_name + '\n'
            # f.write(iid.encode());  f.write(year.encode()); f.write(movie_names.encode())
            page = dict()
            page["_id"] = movie_id; page["movie_name"] = movie_name.replace('.',' ');   page["year"] = i
            if flag == 1:
                browser.close()
                data_save(page)
                print("(_id + movie_name + year) only")
                print()
                # f.close()
                continue
            final_url += 'fullcredits.html'
            print(final_url)
            browser.get(final_url)
            sleep(1)
            content = browser.execute_script("return document.documentElement.outerHTML")
            browser.close()


            # 導演特判
            actor = re.findall('<div class="credits_l">(.*?)</dl>                </div>            </div>',
                               content)
            ele1 = dict()
            try:
                actor = re.findall('<h3><a href="(.*?)" target="_blank">(.*?)</a></h3>', actor[0])
                for a in actor:
                    ele1[a[1].replace('.',' ')] = a[0].replace('.',' ')
                if ele1 == {}:
                    continue
                # f.write(str("演员：").encode())
                # f.write(str(ele1).encode())
                # f.write('\n'.encode())
                page["演员"] = ele1
            except:
                pass



            # 編導特判
            ele2 = dict()
            Director = re.findall(
                '<div class="credits_list">.*?<h4>导演 Director</h4>(.*?)</p>                            </div>                    </div>',
                content)
            try:
                Director = re.findall('<a target="_blank" href="(.*?)">(.*?)</a>', Director[0])
                for a in Director:
                    ele2[a[1].replace('.',' ')] = a[0].replace('.',' ')
                if ele2 == {}:
                    continue
                # f.write(str("导演：").encode())
                # f.write(str(ele2).encode())
                # f.write('\n'.encode())
                page["导演"] = ele2
            except:
                pass



            # 其他人員
            mems = re.findall(
                '<div class="credits_list">                                                <h4>(.*?)</h4> ',
                content)
            for mem in mems[1:]:
                lines = re.findall('<div class="credits_list">                                                <h4>%s</h4>(.*?)</div>' % mem,
                                   content)
                ele3 = dict()
                try:
                    line = re.findall(' <a target="_blank" href="(.*?)">(.*?)</a>', lines[0])
                    if line == "":
                        continue
                    # f.write((str(mem) + "：").encode())
                    for a in line:
                        ele3[a[1].replace('.',' ')] = a[0].replace('.',' ')
                    # f.write(str(ele3).encode())
                    # f.write('\n'.encode())
                    mem = mem.split(' ')[0]
                    page[str(mem)] = ele3
                except:
                    pass

            data_save(page)
            # f.close()
        print("DONE")
        print()

if __name__ == '__main__':
    creater()


