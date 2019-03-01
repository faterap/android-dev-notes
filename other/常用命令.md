# 常用shell命令

## push APK
adb root
adb remount
adb push TPCamera.apk /system/app/TPCamera

## adb logcat
adb logcat -c

## switch JDK
sudo update-alternatives --config java
sudo update-alternatives --config javac

## unzip
tar -xvf file.tar //解压 tar包
tar -xzvf file.tar.gz //解压tar.gz
tar -xjvf file.tar.bz2   //解压 tar.bz2
tar -xZvf file.tar.Z   //解压tar.Z
unrar e file.rar //解压rar
unzip file.zip //解压zip

## switch jdk
sudo update-alternatives --config java
sudo update-alternatives --config javac

## git hook
scp -P 29418 -p tanminghui@mobilegit.rd.tp-link.net:/hooks/commit-msg .git/hooks/n

## git command 
http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html
http://www.linux.org/

## Android 源码编译内容扩容
export JACK_SERVER_VM_ARGUMENTS="-Dfile.encoding=UTF-8 -XX:+TieredCompilation -Xmx4g"
./prebuilts/sdk/tools/jack-admin kill-server
./prebuilts/sdk/tools/jack-admin start-server

## systrace 
python systrace.py --time 3 -a com.tplink.filemanager -o mytrace.html gfx wm am res freq sched perf app disk power

## time for starting activities
adb shell am start -W com.tplink.filemanager/com.tplink.filemanager.activities.FileViewActivity 

## search MediaStore.File
adb shell content query --uri content://media/external/file
可以根据需要把file替换为video/audio/images+/media

adb shell content query --uri content://media/external/file/media/external/file
adb shell content query --uri content://downloads/my_downloads
adb shell content query --uri content://media/internal/file > mtklog/internal_file.txt

## debug before application start
adb shell am set-debug-app -w packagename

## 填充内部存储
adb shell dd if=/dev/zero of=/mnt/sdcard/bigfile