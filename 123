from selenium.webdriver.common.action_chains import ActionChains
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By
from selenium_stealth import stealth
from selenium import webdriver

from colorama import Fore
from typing import Union
import pyperclip
import colorama
import requests
import datetime
import typing
import random
import pprint
import time
import copy
import json
import glob
import os


colorama.init()
#! ------------------------ OTHERS ----------------------- !#
def checkArguments(func_name: str, argument: Union[str, list], an: str) -> None:
    if not bool(argument):
        exit(code=f'{Fore.RED}[log]({func_name} func): {an} is empty')

#! ---------------------- VARIABLES ---------------------- !#
chrome_driver = "v2\\chromedriver.exe"
driver = ''
commands_from_txt_file = []
cftf_string = ''
headers = []
uheaders = []
dheaders = []
twah = '' #? twah - Text With All Headers
respond_file_name = ''


#! -------------- JASPER RELATED FUNNCTIONS -------------- !#
def openJasper():
    if driver.title != 'Jasper Chat - Jasper':
        try:
            driver.get('https://beta.jasper.ai/chat')
            print(f'{Fore.GREEN}[log]: opened "Jasper Chat" successully{Fore.WHITE}')
        except Exception as e:
            exit(code=f'{Fore.RED}[log]: can\'t open "Jasper Chat" -> {e}{Fore.WHITE}\nexit...')

def connectToChrome():
    global driver
    print(f'\n{Fore.YELLOW}[log]: INFO -> open "start_chrome" file in this directory{Fore.WHITE}\n')
    try:
        chrome_options = Options()
        chrome_options.add_experimental_option("debuggerAddress", "localhost:9222")
        chrome_options.add_argument("--headless=new")
        chrome_options.add_argument("--disable-gpu")
        driver = webdriver.Chrome(options=chrome_options)
        chrome_service = Service(executable_path=chrome_driver)

        print(f'{Fore.GREEN}[log]: connected successully{Fore.WHITE}')
    except Exception as e:
        exit(code=f'{Fore.RED}[log]: failed to connect -> {e}\nexit...')

def sendCommandToJasper(msg: list, mode: str):
    global driver, uheaders

    txtarea = driver.find_element(By.TAG_NAME, 'textarea')
    txtarea.clear()
    
    if mode == 'filter_headers':
        try: driver.find_element(By.XPATH, '/html/body/div[1]/div[2]/div/div[2]/div/div[2]/form/div[2]/div/div[1]/div/span/button/span/span[1]').click() 
        except NoSuchElementException: print(f'{Fore.YELLOW}[log](sendCommandToJasper): chat is already empty{Fore.WHITE}')
        time.sleep(2)
        print('message to send:', msg)
        tts = 'remove semantic duplicates from the list below and leave only unique ones: ' #? tts - Text To Send
        counter = 0
        for i in headers:
            counter += 1
            if counter == len(headers):
                tts += f'{i}'
            else: tts += f'{i}, '
        
        print(tts)
        txtarea.send_keys(tts)
        time.sleep(3)
        driver.find_element(By.XPATH, '/html/body/div[1]/div[2]/div/div[2]/div/div[2]/form/div[2]/div/div[1]/span/button').click() #* send msg
        print('waiting')
        time.sleep(17)              #* waiting 17s to let Jasper generate respond
        try:
            elem = driver.find_element(By.XPATH, '/html/body/div[1]/div[2]/div/div[2]/div/div[1]/div/ul/li[2]/div/span[1]/div/p')
            if elem == 'Completion still processing... Refresh the page to see the latest in just a few moments.':
                print('[log](sendCommandToJasper func): TEXT DIDN\'T HANDLED BY JASPER')
                print('[log](sendCommandToJasper func): stoped working for an hour, then exit')
                time.sleep(3600)
                exit(code='exit...')
        except Exception as e:
            pass
        print(f'{Fore.YELLOW}[log](sendCommandToJasper func): got respond successfully')
        driver.find_element(By.XPATH, '/html/body/div[1]/div[2]/div/div[2]/div/div[1]/div/ul/li[2]/div/span[2]/div/span[5]/button').click()
        respond = pyperclip.paste() #* getting respond

        print('-------------respond-------------')
        print(respond)

        # if respond.count(',') > respond.count('\n'):
        #     for i in headers:
        #         if i in respond:
        #             uheaders.append(i)
        # else:
        for i in respond.split('\n'):
            print(i)
            uheaders.append(i)
        print(len(uheaders))
        print(len(headers))
        print(f"{Fore.RED}{headers}")

        # print(f'[log]: writing respond to ', 'respond_' + respond_file_name.split("\\")[-1])
        # with open('respond_' + respond_file_name.split("\\")[-1]+'.txt', 'a') as respond_file:
        #         text = f'{respond.strip()}\n-----------\n'
        #         respond_file.write(text)

    elif mode == 'send_command':
        for i in msg:
            print('message to send:', i)
            
            time.sleep(3)
            driver.find_element(By.XPATH, '/html/body/div[1]/div[2]/div/div[2]/div/div[2]/form/div[2]/div/div[1]/span/button').click()
            time.sleep(17)              #* waiting 17s to let Jasper generate respond
            txtarea.send_keys(f"{i}\n") #* writing & sending prompt
            respond = driver.find_elements(By.CSS_SELECTOR, '#message--NX6mMcyQHCN0uplT-_V > span.relative.pr-4.-mb-3.block > div > ol')  #* getting respond
        
            for i in respond:
                print(i.text)

#! ------------ TEXT FILES RELATED FUNNCTIONS ------------ !#
def getCommands(path=''):
    global cftf_string
    
    if not bool(path):
        checkArguments(func_name='getCommands', argument=path, an='path')

    try:
        commands_from_txt_file.clear()
        with open(f'{path}', 'r') as ftr: #? ftr - File To Read 
            txt = ftr.read()
            cftf_string = txt

            for indx, i in enumerate(txt.split('---------------')):
                i = i.replace('\n', ' ')
                if i == '' or i == ' ':
                    txt.split('---------------').pop(indx)
                    continue
                commands_from_txt_file.append(i.strip())
    except (FileNotFoundError, FileExistsError):
        print(f'{Fore.RED}INVALID INPUT\nENTER VALID PATH WITH TEXTS{Fore.WHITE}')

def getHeaders(cf = []): #? cf - Command File
    if not bool(cf):
        checkArguments(func_name='getHeaders', argument=cf, an='Command File')

    #* extracting all H2 headers from cf
    for item in cf:
        if item.count('H2') < 2:
            continue
        if 'H2' in item and 'H3' not in item:
            item = item.split(' ')
            accept = False
            string = ''
            final = ''
            counter = 0
            words = 0
            gate = 0
            for elem in item:
                words += 1
                if 'H3' in elem or 'include' in elem:
                    continue
                if gate == 0:
                    if 'H2' in elem or accept:
                        accept = True
                        if 'H2' in elem:
                            counter += 1
                            if counter == 2:
                                accept = False
                                continue
                        string += f"{elem} "
                        final = string.strip().split(' ')
            try:
                if 'H2' in final[0]:
                    final.pop(0)
                    final = " ".join(final)
                final = final.replace('вЂ™', '\'')
            except Exception as e:
                print('warning')
            if '/' in final:
                final = final.split('/')[random.randint(0, len(final.split('/'))-1)].strip()
            headers.append(final)
            print(final, f"{Fore.GREEN}appended ✅︎{Fore.WHITE}")

def filterHeaders(headers=[]):
    global twah
    counter = 0
    twah = ''
    if not bool(headers):
        checkArguments(func_name='filterHeaders', argument=headers, an='headers')

    for i in uheaders:
        headers.append(i)
    headers.reverse()
    for i in headers:
        if headers.count(i) == 2:
            headers.remove(i)
        elif headers.count(i) > 2:
            while headers.count(i) > 1:
                headers.remove(i)
    headers.reverse()

    for i in headers:
        counter += 1
        if counter == len(headers): twah += f'{Fore.YELLOW}{counter}. {Fore.WHITE}{i}'
        else                      : twah += f'{Fore.YELLOW}{counter}. {Fore.WHITE}{i}\n'
    counter = 0
    print(twah)

def chooseHeadersToDelete(uh: list, h: list):
    global dheaders

    indices = []
    counter = 0
    rstr = ''
    hdrs = []
    unis = []

    #* filtering headers
    for i in h:
        if i not in uh:
            unis.append(i)
    
    print('len of unis', len(unis))

    print(f'{Fore.GREEN}{unis}')
    print(f'{uh}{Fore.WHITE}')

    uh.extend(unis)

    for i in uh:
        if not bool(i.strip()):
            continue
        counter += 1
        if counter != len(h):
            rstr += f'{Fore.YELLOW}{counter}.{Fore.WHITE} {i}\n'
            hdrs.append(i)
        else:
            rstr += f'{Fore.YELLOW}{counter}.{Fore.WHITE} {i}'
            hdrs.append(i)

    print(rstr)
    print('вам нужно перечислить номера заголовков через пробел: 1 12 2 4 10')
    removeheaders = input(f'{Fore.YELLOW}выберите заголовки которые вы хотите удалить: ')

    print(hdrs)
    print(f'{Fore.YELLOW}{headers}')
    for elem in removeheaders.split(' '):
        print(hdrs[int(elem)-1], hdrs.index(hdrs[int(elem)-1]))

        dheaders.append(hdrs[int(elem)-1])
        hdrs[int(elem)-1] = ' '
    print(hdrs)

    rstr = ''
    counter = 0
    for i in hdrs:
        counter += 1
        rstr += f'{Fore.YELLOW}{counter}.{Fore.WHITE} {i}\n'
    print(rstr)    

    for indx, elem in enumerate(commands_from_txt_file):
        for j in dheaders:
            if j in elem:
                indices.append(indx)
    indices.sort(reverse=True)
    for i in indices:
        commands_from_txt_file.pop(i)

    print(commands_from_txt_file)
    print(len(commands_from_txt_file))

    quit()


def main():
    global headers, commands_from_txt_file, respond_file_name
    connectToChrome()
    openJasper()

    spwf = input('введите путь к папке с txt файлами: ') #? spwf - Select Path With Files
    for file in glob.glob(f"{spwf}/*.txt"):
        respond_file_name = file
        #* cleaning all transfer variables
        commands_from_txt_file.clear()
        headers.clear()
    
        print(f'{Fore.YELLOW}current file:{Fore.WHITE} {file}')
        getCommands(path=file)
        getHeaders(cf=commands_from_txt_file)
        print(headers)
        filterHeaders(headers=headers)
        sendCommandToJasper(msg=headers, mode='filter_headers')
        chooseHeadersToDelete(uh=uheaders, h=headers)
        quit()

if __name__ == '__main__':
    main()
#| coded by c0dem
