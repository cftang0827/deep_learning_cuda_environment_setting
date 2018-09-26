# deep_learning_cuda_environment_setting

## 本文主要是紀錄deep learning使用cuda and cuDnn作為加速的環境建置，並且使用tensorflow keras作為測試環境用

### 環境
- 作業系統: Ubuntu server 16.04
- Linux kernel: 4.4.0-131-generic
- GPU: Nvidia 1080Ti
- CUDA version: cuda 9.0
- CuDnn version: v7.3.0 Library for Linux
- Python environment: Anaconda python3.6

為什麼會選擇Ubuntu server 16.04作為開發環境呢？ 因為截至目前(2018/9), Cuda driver只有支援Ubuntu 16.04以及17，最新的18是沒有支援的，此外，由於目前的Ubuntu 16.04 Desktop預載的kernel都是4.15或是以上，而CUDA 9.0支援的核心是4.4

此外如果是使用Desktop版本安裝也是要做sudo lightdm stop把UI停掉，綜合以上，我認為直接先用server安裝是比較方便的，如果需要UI最後再安裝即可

參考資料: https://docs.nvidia.com/cuda/cuda-installation-guide-linux/


### 安裝步驟
1. 檢查Linux kernel version
```
uname -r
```

2. 檢查nouveau 顯示driver是否存在，如果存在要移除

```
lsmod | grep nouveau
```
如果有東西，可以用以下指令移除
```
sudo vi /etc/modprobe.d/blacklist-nouveau.conf
```
在這個文件最後加上兩行
```
blacklist nouveau
options nouveau modeset=0
```
改完以後下
```
sudo update-initramfs –u
```
最後重開機以後，再用
```
lsmode | grep nouveau
```
檢查是否為空

3. 安裝gcc
```
sudo apt-get install build-essential
```


4. 下載Cuda run file和cudnn library

- CUDA: https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run
- cuDNN: 要先登入Nvidia developer acoount，如果是使用遠端ssh，建議使用chrome插件(curlwget)可以複製瀏覽器的下載URL，直接貼上ssh 遠端terminal就可以下載

CUDA run file記得用
```
sudo chmod +x cuda_9.0.176_384.81_linux-run
```
改變安裝檔案權限
```
sudo ./cuda_9.0.176_384.81_linux-run
```
安裝基本上就是accept，但是有兩個不要選
```
a. Do you want to install the OpenGL libraries? n
b. Do you want to run nvidia-xconfig? n
```
因為根據之前經驗安裝openGL在UI會出現一些問題，剩下的都是yes，如果安裝出現錯誤可以看他出現的log

5. reboot

6. unzip cudnn tgz file
```
tar zxvf cudnn-9.0-linux-x64-v7.3.0.29.tgz
```
然後把對應的lib include丟到對應的地方
```
sudo cp cuda/lib64/* /usr/local/cuda-9.0/lib64/
sudo cp cuda/lib64/* /usr/local/cuda/lib64/
sudo cp cuda/include/* /usr/local/cuda-9.0/include/
sudo cp cuda/include/* /usr/local/cuda/include/
```

7. 檢查nvidia driver是否存在

```
nvidia-smi
```
應該可以看到你的GPU使用率以及VRAM大小資訊

8. 安裝Anaconda
下載anaconda installer 64 bit python3.6
```
wget https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh
```
不要用sudo 安裝anaconda

```
chmod +x Anaconda3-5.2.0-Linux-x86_64.sh
./Anaconda3-5.2.0-Linux-x86_64.sh
```

9. 安裝Tensorflow and Keras
```
source ~/.bashrc #更新bash path，使Anaconda python覆蓋掉原本的Python
```
檢查python從哪來的
```
which python
which pip
```
要都是anaconda來的才是對的

```
conda install tensorflow-gpu -y
conda install keras
```

10. 測試Keras MNIST範例是否使用GPU執行
```
wget https://raw.githubusercontent.com/keras-team/keras/master/examples/mnist_cnn.py

python mnist_cnn.py
```
再來開另外一個terminal看gpu使用率
```
watch -n 1 nvidia-smi
```
應該會看到GPU全速運轉

參考資料: https://blog.csdn.net/QLULIBIN/article/details/78714596


11. 安裝GUI desktop
因為這個是server version，所以沒有GUI支援，如果需要的話要另外安裝
```
sudo apt-get install ubuntu-desktop
```

