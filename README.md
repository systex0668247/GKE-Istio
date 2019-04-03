# 安裝 beta release of Istio on GKE 
---
1. 這是一篇學習用的指引，跟你現在的雲端環境可能會有不同，若是有所改變或失敗，請再通知作者修訂。
2. 底下的指引都會在 Google Cloud Shell 上佈署Istio，無需在自己的電腦安裝相關套件。
3. Google Cloud Shell 環境在閒置一段時間後可能會被消滅，以節省雲端資源。故請選擇可專注的2小時完成此指引
4. 執行指引後的Istio/Kuberenetes環境仍會存在。預設的安裝有 1 台VM做 Kubernetes 叢集，將會產生帳單。建議有免費試用額度的 GCP 帳號為佳。

## 前置作業
---
   1. 需有GCP的帳號，有免費試用額度為佳。
   2. 到[GCP畫面](https://console.cloud.google.com/home/dashboard)上點選由上角的畫面 <br>
       <img src="imgs/shell.jpg" width = "40%" />
   3. 貼上下面指令
   
``` 
cd ~
git clone https://github.com/systex0668247/GKE-Istio.git
cd GKE-Istio
cloudshell launch-tutorial tutorial.md
```

參考網站：(https://codelabs.developers.google.com/codelabs/cloud-hello-istio/index.html?index=..%2F..index#0)
