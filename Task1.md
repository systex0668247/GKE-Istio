
## Task 1 環境建置 - 安裝 GKE

接下來的動作，將會協助您安裝與設定 Google Cloud Platform。您會建立一個新的專案，並安裝後續 Task 所需的 Google Kubernetes Engine(GKE)

## 1. 建立新的專案

### 將專案ID及名稱存為環境參數

```bash
PROJECT_ID=systex-lab-$(cat /proc/sys/kernel/random/uuid | cut -b -6) && \
echo "PROJECT_ID=$PROJECT_ID" | tee -a ~/.profile
```

### 使用 gcloud 指令建立新的專案

```bash
gcloud projects create $PROJECT_ID
```

在 Lab 完成後，您可以直接移除此專案，以省約費用


## 2. 將 Cloud Shell 連接至專案

```bash
gcloud config set project $PROJECT_ID
```

## 3. 設定預設的 region

預設建議為 asia-east1，所在地點為台灣彰濱。

```bash
DEFAULT_REGION=asia-east1 && \
echo "DEFAULT_REGION=$DEFAULT_REGION" | tee -a ~/.profile && \
gcloud config set compute/region $DEFAULT_REGION
```
asia-east1分三個zone (a,b,c) 隨機選擇一個,避免workshop時大家擠在同一個

```bash
GOOGLE_ZONE=asia-east1-$(openssl rand -hex 500|grep -Eo '[a-c]'|head -1) && \
echo "GOOGLE_ZONE=$GOOGLE_ZONE" | tee -a ~/.profile
```

## 4. 設定專案連結帳戶

您必需要為新建的專案連結付費帳戶設定，您可以執行以下指令完成，或至web page[Link a billing account](https://console.developers.google.com/billing/linkedaccount)

### 找尋已設定可用的 Billing account

```bash
BILLING_ACCOUNT=$(gcloud beta billing accounts list | grep True | awk -F" " '{print $1}')
```

### 設定專案連結付費帳戶

```bash
gcloud beta billing projects link $PROJECT_ID --billing-account $BILLING_ACCOUNT
```
一定要看到類似下面畫面確實綁定BILLING_ACCOUNT 否則後續會有問題
```
xxxxxx@cloudshell:~ (systex-lab-e749a3)$ gcloud beta billing projects link $PROJECT_ID --billing-account=$BILLING_ACCOUNT
billingAccountName: billingAccounts/0xxxx640-9024B6-105FE5
billingEnabled: true
name: projects/systex-lxxxxa3/billingInfo
projectId: systex-lab-e749xx
```

## 安裝 Google 準備的 K8S 叢集

### 啟用 Kubernetes Engin API  

以下指令會啟用所有 Kubernetes Engine 所需要的 API 使用權限，大約需要 2min
；或是至Web page [啟用 Kubernetes Engin API](https://console.cloud.google.com/apis/library/container.googleapis.com)

```bash
gcloud services enable container.googleapis.com
```

### 安裝GKE

以下指令，會協助您建立 K8S 叢集，只有一個 worknodes，完成約需 3min

```bash
gcloud container clusters create ${PROJECT_ID}-k8s \
    --num-nodes=3 \
    --cluster-version=1.11.9-gke.8 \
    --zone=${GOOGLE_ZONE}
```

請上網查看[相容的K8S版本](https://cloud.google.com/istio/docs/istio-on-gke/installing#supported_gke_cluster_versions)，設定到底下的 `--cluster-version`。 

_參數 --num-nodes 設定太大時，可能會請求不到資源_

##  安裝GKE後的設定
1. 授權使用者權限為 GKE的cluster-admin
```bash
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=$(gcloud config get-value core/account)
```

2. 取得 K8S 叢集認證
```bash
gcloud container clusters get-credentials $PROJECT_ID-k8s --zone=${GOOGLE_ZONE}
````

3. 查看 K8S 叢集
```bash
gcloud container clusters list
```
 
