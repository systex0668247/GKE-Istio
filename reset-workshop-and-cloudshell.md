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
