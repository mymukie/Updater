#Updater
# This program is create on my purpose only 
# Program will check the version from *.ini* file from network,path folder compares with ini config from local program
# copy all items inside path/folder.
# you have to create "Pyconfig.ini" on source that contains 
'''
  [DEFAULT]
updatepath = \\FILE-DTL03\100fada\140Production Control\Production Control System\Pupdate
updatelocation = C:\SIT

[VER]
version = 29032022 = > version will change from this if you have an update by change what ever you like.
'''
# from program will be create by itself from script code. 

from the program can be config source and destination inside the program.

to change an execute file name look into this line

def exitpy():
    CheckUpdate_button['state']='normal'
    runcfg = cf.ConfigParser()
    runcfg.read('Pyconfig.ini')
    exePath = runcfg['DEFAULT']['updateLocation']
    os.chdir(exePath)
 >>   pathos = exePath +'\program.exe' << change to the exe file to open after update.
    print(pathos)
    os.startfile(pathos)
    os._exit(0)
    
    
# if there is no update from source means Pyconfig.ini ['VERSION'] = no change the program will start automatically.
