## 如果因為中途失敗或是整個LAB已完成想再重做一次

### 刪除專
```bash
gcloud projects delete $PROJECT_ID
```

### 刪除cloudshell profile檔
```bash
cp -rf ~/GKE-Istio/profile ~/.profile
```

### 刪除github 下載的檔案
```bash
rm -rf ~/GKE-Istio
```
