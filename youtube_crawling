import requests
from bs4 import BeautifulSoup
import re
import pickle
from selenium import webdriver as wd
import time
from selenium.webdriver.chrome.options import Options


options = Options() 
options.binary_location= 'C:/Program Files/Google/Chrome/Application/chrome.exe'


urlDic = {}

def removeTag(string):
    removeMode = False
    removeCount = 0
    for i, t in enumerate(string):
        if t=="<":
            removeMode = True
        if removeMode:
            if t==">":
                removeMode = False
            string = string[0:i-removeCount] + string[i + 1 - removeCount:]
            removeCount+=1
    return string

url = "https://www.youtube.com/watch?v=tkKhkJq14Eg"
#url = "https://www.youtube.com/watch?v=vEzW9X15HHE"


headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36'}
headers = {'User-Agent':'Chrome/66.0.3359.181'}
headers = {'User-Agent':'Mozilla/5.0', 'referer' : 'http://www.naver.com'}
headers = {'User-Agent':'Mozilla/5.0'}

driver = wd.Chrome(executable_path="C:/Users/gkwns/Desktop/cvpr/중소기업/chromedriver_win32/chromedriver.exe", chrome_options = options)

def crawingContent(num):
    global url
    response = requests.get(url, headers=headers)
    driver.get(url)
    last_page_height = driver.execute_script("return document.documentElement.scrollHeight")
    if response.status_code == 200:
        try:
            while True:
                driver.execute_script("window.scrollTo(0, document.documentElement.scrollHeight);")
                time.sleep(1.0)   
                new_page_height = driver.execute_script("return document.documentElement.scrollHeight")
            
                if new_page_height == last_page_height:
                    break
                last_page_height = new_page_height
    
            # 크롤링
            page = driver.page_source
            soup = BeautifulSoup(page, 'lxml')
            contents = soup.select('#content-text')
            content = ""
            for c in enumerate(contents):
                content += "".join(list(map(str,c)))
            
            title = soup.select_one('#container > h1 > yt-formatted-string')
            title = list(map(str,title))\
                
            nextUrl = soup.select_one('#dismissible > ytd-thumbnail')
            nextUrl = list(map(str,nextUrl))
            for u in nextUrl:
                if 'href=' in u:
                    nextUrl = u.split("href=")[1]
                    nextUrl = nextUrl.split(" ")[0]
                    break
            
           # 주석제거
            for t in content:
                if "//" in t:
                    content.remove(t)
            content = "".join(content)
            title = "".join(title)
            
            # 소괄호를 공백문자로 변경
            content = content.replace("(", " ")
            content = content.replace(")", " ")
            
            # 태그제거
            content = removeTag(content)
            title = removeTag(title)
                    
            # 한글제외 문자제거
            hangul = re.compile('[^ ㄱ-ㅣ가-힣]+')
            content = hangul.sub(' ', content)
            title = hangul.sub(' ', title) 
            

            urlDic[title] = content
            
            url = "https://www.youtube.com"+nextUrl[1:-1]
            
        except:
            print("error")
            pass
        
    else : 
        print("error: ",response.status_code)
        return -1
    
    
    


def main():
    #0011074062
    for num in range(3):
        code = crawingContent(num)
        if code == -1:
            break
        
        if num%100==0:
            print("num: ", num)
            
    if len(urlDic)>0:
        with open("C:/Users/gkwns/Desktop/cvpr/중소기업/크롤링/20210702.txt","wb") as fw:
            print(urlDic)
            pickle.dump(urlDic, fw)

main()


