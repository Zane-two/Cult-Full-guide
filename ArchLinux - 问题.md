# ArchLinux

## 1. 音量设置笔记

### (1).   pulseaudio

开启系统声音 

```
pacman -S alsa alsa-utils kmix
```

命令行管理音量

```
alsamixer
```

图形界面音量管理

```
kmix
```


xfce4:

https://wiki.archlinux.org/index.php/PulseAudio

### (2).解决方法

1. 安装 pulseaudio

```
PulseAudio serves as a proxy to sound applications using existing kernel sound components like ALSA or OSS.
```

2. 安装 pavucontrol

```
There are a number of front-ends available for controlling the PulseAudio daemon:    GTK GUIs: paprefs and pavucontrol 
```


安装后：

$：pulseaudio --start        //--kill关闭 作者：shiyue-001 https://www.bilibili.com/read/cv12709685 出处：bilibili