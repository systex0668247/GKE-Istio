# 安裝 beta release of Istio on GKE 
---
1. 這是一篇學習用的指引，跟你現在的雲端環境可能會有不同，若是有所改變或失敗，請再通知作者修訂。
2. 底下的指引都會在 Google Cloud Shell 上佈署Istio，無需在自己的電腦安裝相關套件。
3. Google Cloud Shell 環境在閒置一段時間後可能會被消滅，以節省雲端資源。故請選擇可專注的2小時完成此指引
4. 執行指引後的Istio/Kuberenetes環境仍會存在。預設的安裝有 1 台VM做 Kubernetes 叢集，將會產生帳單。建議有免費試用額度的 GCP 帳號為佳。

## 前置作業
---
   1. 需有GCP的帳號，有免費試用額度為佳。
   2. 建立GCP新專案，或使用預設/既存的專案。若是自學測試用，建議採用新專案，可以測試後刪除整個專案，避免產生意外費用。
   3. 確認所要使用的專案代號，等會的步驟中會要從下拉選單中選取專案。
   4. 按底下的按鈕開啟你的 GCP Cloud Shell

[![button](http://gstatic.com/cloudssh/images/open-btn.png)](https://console.cloud.google.com/cloudshell/open?git_repo=https://github.com/harryliu123/GKE-Istio&page=shell&working_dir=GKE-Istio&tutorial=tutorial.md)

參考網站：(https://codelabs.developers.google.com/codelabs/cloud-hello-istio/index.html?index=..%2F..index#0)
