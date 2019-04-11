## 如果因為中途失敗或是整個LAB已完成想再重做一次

### Delete Project
```bash
gcloud projects delete $PROJECT_ID
```

### Reset cloudshell profile
```bash
cp -rf ~/GKE-Istio/profile ~/.profile
```

### Delete github Download Files
```bash
rm -rf ~/GKE-Istio
```


#### Debug :
如果發現出現istio所產生的pod 無法順利產生如下圖:
![fail_get_istio_pod.jpg](imgs/fail_get_istio_pod.jpg)
可能是billing-account 沒有綁定成功
請重新執行綁定
```
kubectl delete -f ~/istio.yaml
gcloud beta billing projects link $PROJECT_ID --billing-account=$BILLING_ACCOUNT
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=$(gcloud config get-value core/account)
kubectl apply -f ~/istio.yaml
```
