# 安裝 beta release of Istio on GKE
## 注意事項
---
1. 這是一篇學習用的指引，跟你現在的雲端環境可能會有不同，若是有所改變或失敗，請再通知作者修訂。
2. 底下的指引都會在 Google Cloud Shell 上佈署Istio，無需在自己的電腦安裝相關套件。
3. Google Cloud Shell 環境在閒置一段時間後可能會被消滅，以節省雲端資源。故請選擇可專注的2小時完成此指引
4. 執行指引後的Istio/Kuberenetes環境仍會存在。預設的安裝有 1 台VM做 Kubernetes 叢集，將會產生帳單。建議若是要暫停多日時，可以先刪除專案，以避免產生意外的費用。
5. 需有GCP的帳號，有免費試用額度為佳。

## 前置作業
---
 
   1. 千萬別對開啟 cloudshell的GCP畫面按 F5 refresh, 因為cloudshell會重置, 如果需要畫面 refresh 請利用頁面上重新整理完成
   2. 休息期間 別將browser 關閉, 因為cloudshell會重置
   3. 到[GCP畫面](https://console.cloud.google.com/home/dashboard)上點選由上角的畫面 <br>
       <img src="imgs/shell.jpg" width = "40%" />
   4. 貼上下面指令
   
``` 
cd ~
rm -rf GKE-Istio
git clone https://github.com/systex0668247/GKE-Istio.git
```

### Task1 安裝 GKE
```
cloudshell launch-tutorial ~/GKE-Istio/Task1.md
```

### Task2 Helm 部屬 Istio
```
cloudshell launch-tutorial ~/GKE-Istio/Task2.md
```
   
### Task3 kiali port-forword
```
cloudshell launch-tutorial ~/GKE-Istio/Task3.md
```

### Task4 藍綠部屬
```
cloudshell launch-tutorial ~/GKE-Istio/Task4.md
```

### Task5 	Delay & Fault Injection
```
cloudshell launch-tutorial ~/GKE-Istio/Task5.md
```

### Task6 刪除專案
```
cloudshell launch-tutorial ~/GKE-Istio/Task6.md
```


### reset-workshop-and-cloudshell
```
cloudshell launch-tutorial ~/GKE-Istio/reset-workshop-and-cloudshell_debug.md
```

</br>


參考網站：(https://codelabs.developers.google.com/codelabs/cloud-hello-istio/index.html?index=..%2F..index#0)


