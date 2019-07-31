      Yolo v3 install
 
    事前準備:
          1. 確定Nvidia Driver是否正常  nvidia-smi
          2. 確定Cuda 是否正確安裝       nvcc --version
          3. 確定將照片轉成 txt檔案 並且把照片跟txt檔案放在一起(PS:名稱要一樣，副檔名不一樣即可)


DarkNet install:


1.  git clone https://github.com/pjreddie/darknet.git
2.  cd darknet
3.  make   (如果編譯有成功會看到下面的圖)
     
     ps:會有一大堆訊息





4. ./darknet (測試是否正確編譯)
      
output





接下來就是要開始更改裡面的參數以及新增一些檔案


 5.  cd darknet
 6.  vim Makefile
 7.  找到文件的第一行 把 GPU = 0 更改成 GPU = 1 
 8.  make (重新編譯整個檔案)
 9.  vim split ( 在 darknet 的資料夾中 創造一個 split.py的檔案) 接著複製貼上下面的 code


         
import glob
import os
import Tkinter
import Tkconstants
import tkFileDialog
while True:
    print("Please select your image directory.")
    current_dir = tkFileDialog.askdirectory()
    if current_dir == None or current_dir == "":
        print("You must select a directory.")
        continue
    break

記得要存檔 (:wq)


10.     wget https://pjreddie.com/media/files/darknet53.conv.74 (下載 捲積圖層的權重到darknet)
11.    vim obj.data (定義要訓練的資料)加入下面的code  PS: 10，11創建在dark的最外層即可




classes = 1            
train = train.txt
valid = test.txt
names = obj.names
backup = backup/


classes ------->你要訓練的類別(個數)
train---------->訓練的txt檔案(darknet只用這種檔案訓練)
valid---------->測試資料集的txt檔案
names-------->
backup------->訓練資料集的還原點


記得要在darknet底下創建 obj.names的文件


 
12.    python split.py   (進行連結到照片&照片txt的位置     PS:這邊會有視窗可以選擇 ) 沒有tkinter的話要裝 sudo apt-get install python-tk          
13.    cd cfg
14.    cp yolov3.cfg obj.cfg
15.    vim obj.cfg (開始更改訓練的詳細參數)
         step1: line3 batch=24
         step2: line4 subdivision=8
         step3:   line  603        filters=24      ex:現在要訓練的車種有3種就帶入3   (計算公式: filters=(classes+5)*3)
         step4:   line  610        classes=3      --> your classes
         step5:   line  689        filters=24
         step6:   line  696        classes=3
         step7:   line  776        filters=24
         step8:   line  783        classee=3                 
  
          至於這個參數為什麼是這樣設計不要問我 
       
16.    ./darknet detector train obj.data cfg/obj.cfg darknet53.conv.74   (啟動訓練)               


17.        watch -n 1 nvidia-smi             


18.       sudo ./darknet detector demo cfg/obj.data cfg/obj.cfg ~/Downloads/darknet/backup/obj.backup (以影像做物件辨識)                                  


  
 sudo因為我忘記設定使用者也能用 所以只能用sudo的方式執行 
 ./darknet detector demo(這部份大家都一樣,只是為了啟動相機的部份) 
 cfg/obj.data(訓練資料)
cfg/obj.cfg(_______)
~/Downloads/darknet/backup/obj.backup(訓練權重)           


參考網站     yolo v3 官方網站:   https://pjreddie.com/darknet/yolo/
                         darknet 跟yolov3在同一個網站裡面:   https://pjreddie.com/darknet/install/
                        參數調整網頁：https://towardsdatascience.com/tutorial-build-an-object-detection-system-using-yolo-9a930513643a
