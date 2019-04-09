# 在 Google Cloud Shell 上建置 Istio 


## 新增 or 選擇 GCP 專案

底下請選擇執行作業的專案{{project-id}}。
<walkthrough-project-billing-setup></walkthrough-project-billing-setup>

## 設定專案 ID
1. 將專案ID及名稱存為環境參數。
```bash
export PROJECT_ID={{project-id}}; export PROJECT_NAME={{project-name}}
```
2. 將 Cloud Shell 連接至專案
```bash
gcloud config set project {{project-id}}
```
3. 設定預設的 region (建議為 asia-east1  ,台灣 彰濱)
```bash
gcloud config set compute/region asia-east1
```
4. 至下列網址啟用專案的 Kubernetes Engin API  
```bash
gcloud services enable container.googleapis.com
```
或是至Web page [啟用 Kubernetes Engin API](https://console.cloud.google.com/apis/library/container.googleapis.com?q=kubernetes%20engine&amp;_ga=2.82276438.-1912799670.1549085300)




</br>

</br>

</br>

### 方式二  (不建議, 由於lab 會用到port-forword 會影響 cloudshell)
   請在按底下的按鈕上，用滑鼠右鍵「在新視窗中開啟連結」，開啟你的 GCP Cloud Shell，就是接著的 Lab 操作環境。
   
   [![button](http://gstatic.com/cloudssh/images/open-btn.png)](https://console.cloud.google.com/cloudshell/open?git_repo=https://github.com/systex0668247/GKE-Istio&page=shell&tutorial=tutorial.md)
   
