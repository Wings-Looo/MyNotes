# Mac下编译ijkplayer

编译环境 Mac OS X 10.12.2

### Step 1.
如本地已经NDK请无视。

若本地无NDK,下载NDK。
    
  >  下载方式：
  >
  >  1.通过Android Studio自动下载。通过Command+;打开ProjectStructure，在下方有个Download Android NDK，即可下载最新版本的NDK。
  > 
  >  2.通过官网下载。下载地址:[link](https://developer.android.com/ndk/downloads/index.html#download),此地址为最新版本的NDK。[link](https://developer.android.com/ndk/downloads/older_releases.html),此地址中的NDK皆为老版本的NDK。下载后解压即可。
  >  
  >  示例：本人NDK路径/Users/wings/Library/Android/android-ndk-r10e
    
### Step 2.
打开终端，安装homebrew、git、yasm。若已安装请无视。

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"  
    brew install git  
    brew install yasm 
    
### Step 3.
在.bash_profile中配置NDK。

    open -e ~/.bash_profile   //在终端中打开.bash_profile
    export ANDROID_SDK=<your sdk path>  
    export ANDROID_NDK=<your ndk path>  //在.bash_profile文件中配置SDK以及NDK的路径
    source ~/.bash_profile //配置完成后在终端执行此句
    
    示例：export ANDROID_NDK=/Users/wings/Library/Android/android-ndk-r10e   
         export ANDROID_SDK=/Users/wings/Library/Android/sdk

### Step 4.
从github中clone项目

    git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-android 
    
### Step 5.
编译ffmpeg，此过程由于需要联网下载资源，较耗时
    
    cd ijkplayer-android   
    ./init-android.sh   
    cd config  
    rm module.sh  
    ln -s module-default.sh module.sh 
    cd ..
    cd android/contrib  
    ./compile-ffmpeg.sh clean  
    ./compile-ffmpeg.sh all

### Step 6.
编译ijkplayer
    
    cd .. 
    ./compile-ijk.sh all

此过程编译完成后会在ijkplayer-android文件夹下的android文件夹中生产ijkplayer文件，为一个Android Studio的工程，导入即可。