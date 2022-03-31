# Updater
from pathlib import Path
import os
import tkinter as tk
import configparser as cf
import os.path
import shutil
import ctypes
import threading as th
import time
from tkinter import messagebox as msbox
from tkinter.constants import E
'''
SIT Updater Version 1.0005

without Runscreen when is up to date.
'''
#ctypes.windll.user32.ShowWindow( ctypes.windll.kernel32.GetConsoleWindow(), 6 )


def exitno():
    os._exit(0)
try:
    if os.path.isfile('Pyconfig.ini'):
        print("Pyconfig Exist")
        config = cf.ConfigParser()
        config.read('Pyconfig.ini')
        config.sections()
        #separeate value
        intext = config['DEFAULT']['updatepath']
        outtext = config['DEFAULT']['updatelocation']
        Pver = config['VER']['version']
    else:
        print("NOFILE")
        config = cf.ConfigParser()
        config
        config['DEFAULT'] = {'UpdatePath': "\\\\FILE-DTL03\100fada\140Production Control\Production Control System\Pupdate",
                     'Updatelocation': "C:\SIT"}
        config['VER'] = {'version':"1.004"}
        with open('Pyconfig.ini','w') as configfile:
            config.write(configfile)
        config = cf.ConfigParser()
        config.read('Pyconfig.ini')
        config.sections()
        intext = config['DEFAULT']['updatepath']
        outtext = config['DEFAULT']['updatelocation']
        Pver = config['VER']['version']
except IOError:
        print('ERROR')
        root = tk.Toplevel()
        root.title('Error Connection')
        root.minsize(250,100)
        label = tk.Message(root, text="Error on config file, Please check", width=300)
        label.pack()
        ok_button = tk.Button(root, text="OK", width=15,height=2)
        ok_button.pack(pady=5)
        ok_button['command'] = exitno
        root.protocol("WM_DELETE_WINDOW",exitno)
        root.mainloop()

       
#Config path
def pathConfig():
    config = cf.ConfigParser()
    config.read('Pyconfig.ini')
    rdin = config['DEFAULT']['UpdatePath']
    rdout = config['DEFAULT']['UpdateLocation']
    pathWindow = tk.Tk()
    pathWindow.grid_columnconfigure((0,1),weight=1)
    pathWindow.title('Patch Configure')
    pathWindow.minsize(500,150)

    config_label = tk.Label(pathWindow,text='Update location')
    config_label.grid(row=0,column=0)
    inpath_label = tk.Label(pathWindow,text='Location')
    inpath_label.grid(row=3,column=0)
    inpath_entry = tk.Entry(pathWindow, width=50)
    inpath_entry.grid(row=3,column=1, sticky="ew")
    outpath_label = tk.Label(pathWindow,text='Destination')
    outpath_label.grid(row=4,column=0)
    outpath_entry = tk.Entry(pathWindow, width=50)
    outpath_entry.grid(row=4,column=1, sticky="ew")
    def saveDir():
        try:
            newPath = inpath_entry.get()
            newLocate = outpath_entry.get()
            config.set('DEFAULT','UpdatePath', newPath)
            config.set('DEFAULT','UpdateLocation',newLocate)
            with open('Pyconfig.ini','w') as configfile:
                config.write(configfile)
            print("Saved")
            config.read('Pyconfig.ini')
            inpath_entry.insert(0,rdin)
            outpath_entry.insert(0,rdout)
            recheck()
            pathWindow.destroy()
        except:
            print('ERROR')

    

    save_button = tk.Button(master=pathWindow, text='SAVE',
                               command=saveDir,width=15, height=2)
    save_button.grid(row=5,column=0,sticky="ew")
    exit_button = tk.Button(master=pathWindow, text='EXIT',
                                command=pathWindow.destroy,width=15, height=2)
    exit_button.grid(row=5,column=1, sticky="ew")
    
    inpath_entry.insert(0,rdin)
    outpath_entry.insert(0,rdout)
    
    pathWindow.mainloop()


    
window = tk.Tk()
window.title('SIT UPDATE')
window.minsize(200, 150)
ll = tk.Label(window, text='SIT PROGRAM UPDATE')
ll.pack()



def checkUpdate():
    if CheckUpdate_button['text']=='UPDATE':
       CheckUpdate_button['text']='UPDATING...'
    else:
        CheckUpdate_button['text']='UPDATE'
        
    print("Checking..")
    print('ok')
    
    try:
        
        config.read('Pyconfig.ini')
        currentVer = config['VER']['version']
        cfpath = config['DEFAULT']['updateLocation']
        cflocate = config['DEFAULT']['updatePath']
        cfg = cf.ConfigParser()
        cfg.read(cflocate + "\Pyconfig.ini")
        verUpdate = cfg['VER']['version']

    except:
        print('ERROR')
        root = tk.Toplevel()
        root.title('Error Connection')
        root.minsize(250,100)
        label = tk.Message(root, text="Can't connect to server", width=300)
        label.pack()
        ok_button = tk.Button(root, text="OK", width=15,height=2)
        ok_button.pack(pady=5)
        ok_button['command'] = exitno
        root.protocol("WM_DELETE_WINDOW",exitno)
        root.mainloop()
    def thread_complete():
        print('SIT is Up to date...')
        config['VER']['version'] = verUpdate
        verPG = config['VER']['version']
        with open('Pyconfig.ini','w') as configfile:
            config.write(configfile)
        print("versions is update to: "+ verPG)
        root = tk.Toplevel()
        root.title('SIT Updated')
        root.minsize(250,100)
        label = tk.Message(root, text="SIT IS UPDATED", width=150)
        label.pack()
        ok_button = tk.Button(root, text="OK", width=15,height=2)
        ok_button.pack(pady=5)
        ok_button['command'] = exitpy
        root.protocol("WM_DELETE_WINDOW",exitpy)
        window.protocol("WM_DELETE_WINDOW",exitpy)
        
    if  currentVer != verUpdate:
        print("SIT has new update...")
        try:
            def thread_copy():
                CheckUpdate_button['text']='UPDATING...'
                CheckUpdate_button['state']='disabled'
                t1 =time.perf_counter()
                shutil.copytree(cflocate,cfpath, ignore=None, dirs_exist_ok=True)
                finish = time.perf_counter()
                print(f'Finished in {finish-t1} sconds')
                thread_complete()
            x = th.Thread(target=thread_copy,)
            x.start()
        except Exception as e:
            msbox.showwarning(title='WARNING', message='ERROR')
            
    else:
        print('SIT is Up to date...')
        exitpy()
    


def exitpy():
    CheckUpdate_button['state']='normal'
    runcfg = cf.ConfigParser()
    runcfg.read('Pyconfig.ini')
    exePath = runcfg['DEFAULT']['updateLocation']
    os.chdir(exePath)
    pathos = exePath +'\Daifuku.exe'
    print(pathos)
    os.startfile(pathos)
    os._exit(0)

    

CheckUpdate_button = tk.Button(master=window, text='RUN',
                               command=checkUpdate,width=15, height=2)
CheckUpdate_button.pack(pady=5)
config_button = tk.Button(master=window, text='CONFIG',
                          command = pathConfig,width=15,height=2)
config_button.pack(pady=10)

try:
            cfgcheck = cf.ConfigParser()
            cfgcheck.read(intext + "\Pyconfig.ini")
            vUp = cfgcheck['VER']['version']
            cVer = config['VER']['version']
            if cVer != vUp:
                ll['text']='SIT HAS UPDATE'
                CheckUpdate_button['text'] ='UPDATE'
                print('UPDATE')
            else:
                exitpy()
except:
    print('ERROR')
    ll['text']='CONNECTION ERROR'

def recheck():
    try:
            localcheck = cf.ConfigParser()
            localcheck.read('Pyconfig.ini')
            updatepath = localcheck['DEFAULT']['updatepath']
            cfgcheck = cf.ConfigParser()
            cfgcheck.read(updatepath + "\Pyconfig.ini")
            vUp = cfgcheck['VER']['version']
            cVer = config['VER']['version']
            if cVer != vUp:
                ll['text']='SIT HAS UPDATE'
                CheckUpdate_button['text'] ='UPDATE'
                print('UPDATE')
            else:
                exitpy()
    except Exception as e: 
        print(e)
        print('ERROR')
        ll['text']='CONNECTION ERROR'



window.mainloop()



