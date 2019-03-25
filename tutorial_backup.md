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

## 安裝 Google 準備的 K8S 叢集 + Istio
請上網查看[相容的K8S版本](https://cloud.google.com/istio/docs/istio-on-gke/installing#supported_gke_cluster_versions)
設定到底下的 cluster name, and [cluster-version]。  
底下兩種安裝選項，關係到 istio 的微服務是否只限於 TLS 加密通訊。為了workshop順利建議選擇第一項，事後可以修改。
1. 不強制 mutual TLS 版本
```bash
gcloud beta container clusters create {{project-id}}-k8s \
    --project=$PROJECT_ID \
    --addons=Istio \
    --istio-config=auth=MTLS_PERMISSIVE \
    --machine-type=n1-standard-2 \
    --region=asia-east1 \
    --num-nodes=1 
```
2. 強制 mutual TLS 版本
```bash
gcloud beta container clusters create {{project-id}}-k8s \
    --project=$PROJECT_ID \
    --addons=Istio \
    --istio-config=auth=MTLS_STRICT \
    --machine-type=n1-standard-2 \
    --zone=asia-east1-a \
    --num-nodes=1 
```

## 驗證 Istio 安裝結果
1. 查看 K8S 叢集
```bash
gcloud container clusters list
```
2. 取得 K8S 叢集認證
```bash
gcloud container clusters get-credentials {{project-id}}-k8s --zone=asia-east1-a
````

3. 驗證 Istio 安裝在命名空間 istio-system 的結果
```bash
kubectl get deployments,ing -n istio-system
```
   應該看到類似的結果
```
NAME                       TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                                                                                                                   AGE
istio-citadel              ClusterIP      10.47.245.92    <none>        8060/TCP,9093/TCP                                                                                                         12s
istio-egressgateway        ClusterIP      10.47.248.129   <none>        80/TCP,443/TCP                                                                                                            12s
istio-galley               ClusterIP      10.47.248.109   <none>        443/TCP,9093/TCP                                                                                                          12s
istio-ingressgateway       LoadBalancer   10.47.248.117   <pending>     80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30221/TCP,8060:32445/TCP,853:30663/TCP,15030:32010/TCP,15031:32633/TCP   12s
istio-pilot                ClusterIP      10.47.251.133   <none>        15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     12s
istio-policy               ClusterIP      10.47.255.244   <none>        9091/TCP,15004/TCP,9093/TCP                                                                                               12s
istio-sidecar-injector     ClusterIP      10.47.240.36    <none>        443/TCP                                                                                                                   12s
istio-statsd-prom-bridge   ClusterIP      10.47.247.135   <none>        9102/TCP,9125/UDP                                                                                                         12s
istio-telemetry            ClusterIP      10.47.242.73    <none>        9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     12s
promsd                     ClusterIP      10.47.241.188   <none>        9090/TCP                                          
```

4. 驗證 K8S Pods 已在運行
```bash
kubectl get pods -n istio-system
```
   應該看到類似的結果
```
NAME                                        READY   STATUS      RESTARTS   AGE
istio-citadel-555d845b65-xfdmj              1/1     Running     0          2d
istio-cleanup-secrets-8x2pl                 0/1     Completed   0          2d
istio-egressgateway-667d854c49-9q5dl        1/1     Running     0          2d
istio-galley-6c9cd5b8bb-4j4jk               1/1     Running     0          2d
istio-ingressgateway-6c796c5594-f972p       1/1     Running     0          2d
istio-pilot-77f74fc6f-rpbfj                 2/2     Running     0          2d
istio-policy-655b87fff-4wbwq                2/2     Running     0          2d
istio-security-post-install-tm2rm           0/1     Completed   1          2d
istio-sidecar-injector-668c9fb4db-p6lwt     1/1     Running     0          2d
istio-statsd-prom-bridge-5b645f6f4d-6pbgf   1/1     Running     0          2d
istio-telemetry-d9848f498-wf6kh             2/2     Running     0          2d
promsd-6b989699d8-l7jxt                 1/1     Running     0          2d
```

## 設定 Istio
1. 在範例 bookinfo 的 namespace <預設為dafault,您也可以自訂> 啟用 sidecar injection
```bash
kubectl label namespace default istio-injection=enabled
```

## 安裝 Istio 範例
1. 下載安裝檔
```bash
cd ~; curl -L https://git.io/getLatestIstio | sh -
```
2. 設定 istio 最新版本之參數
```bash
export ISTIO_LAST=$(ls -d istio-1.*)
```
3. 設定最新版本 istioctl 路徑 (版本會變動，限制於 istio-1.x)
```bash
export PATH="$PATH:~/$ISTIO_LAST/bin" ; cd $ISTIO_LAST
```
4. 以內建編輯器新增 Istio 微服務範例 bookinfo 的安裝 yaml 檔
```bash
touch ~/$ISTIO_LAST/samples/bookinfo/platform/kube/bookinfo-only-have-veviews-v1.yaml
```
```bash
cloudshell edit ~/$ISTIO_LAST/samples/bookinfo/platform/kube/bookinfo-only-have-veviews-v1.yaml
```
5. 修改 bookinfo-only-have-veviews-v1.yaml，內容中的 default 可置換成自訂的 namespace 名稱。
```
apiVersion: v1
kind: Namespace
metadata:
  name: default
---
##################################################################################################
# Details service
##################################################################################################
apiVersion: v1
kind: Service
metadata:
  name: details
  namespace: default
  labels:
    app: details
spec:
  ports:
  - port: 9080
    name: http
  selector:
    app: details
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: details-v1
  namespace: default
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: details
        version: v1
    spec:
      containers:
      - name: details
        image: istio/examples-bookinfo-details-v1:1.10.1
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 9080
---
##################################################################################################
# Ratings service
##################################################################################################
apiVersion: v1
kind: Service
metadata:
  name: ratings
  namespace: default
  labels:
    app: ratings
spec:
  ports:
  - port: 9080
    name: http
  selector:
    app: ratings
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: ratings-v1
  namespace: default
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: ratings
        version: v1
    spec:
      containers:
      - name: ratings
        image: istio/examples-bookinfo-ratings-v1:1.10.1
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 9080
---
##################################################################################################
# Reviews service
##################################################################################################
apiVersion: v1
kind: Service
metadata:
  name: reviews
  namespace: default
  labels:
    app: reviews
spec:
  ports:
  - port: 9080
    name: http
  selector:
    app: reviews
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: reviews-v1
  namespace: default
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: reviews
        version: v1
    spec:
      containers:
      - name: reviews
        image: istio/examples-bookinfo-reviews-v1:1.10.1
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 9080
---
##################################################################################################
# Productpage services
##################################################################################################
apiVersion: v1
kind: Service
metadata:
  name: productpage
  namespace: default
  labels:
    app: productpage
spec:
  ports:
  - port: 9080
    name: http
  selector:
    app: productpage
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: productpage-v1
  namespace: default
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: productpage
        version: v1
    spec:
      containers:
      - name: productpage
        image: istio/examples-bookinfo-productpage-v1:1.10.1
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 9080
---

```
6. 安裝 bookinfo 範例
```bash
kubectl apply -f <(istioctl kube-inject -f samples/bookinfo/platform/kube/bookinfo-only-have-veviews-v1.yaml)
```
```bash
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
```
7. 驗證 bookinfo 安裝
```bash
kubectl get services
```
   應該看到如下結果：
```
NAME                       CLUSTER-IP   EXTERNAL-IP   PORT(S)              AGE
details                    10.0.0.31    <none>        9080/TCP             6m
kubernetes                 10.0.0.1     <none>        443/TCP              7d
productpage                10.0.0.120   <none>        9080/TCP             6m
ratings                    10.0.0.15    <none>        9080/TCP             6m
reviews                    10.0.0.170   <none>        9080/TCP             6m
```
8. 更進一步驗證
```bash
kubectl get deployments,ing
```
   應該看到類似的結果
```
NAME                                   DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/details-v1       1         1         1            1           6m
deployment.extensions/productpage-v1   1         1         1            1           6m
deployment.extensions/ratings-v1       1         1         1            1           6m
deployment.extensions/reviews-v1       1         1         1            1           6m
```
9. 取得bookinfo網址 http://$GATEWAY_URL/productpage 並驗證
```bash
INGRESSGATEWAY=istio-ingressgateway \
&& INGRESSGATEWAY_LABEL=istio
```
```bash
export INGRESS_IP=$(kubectl -n istio-system get service istio-ingressgateway  \
-o jsonpath='{.status.loadBalancer.ingress[0].ip}')
```
```bash
echo http://$INGRESS_IP/productpage
```

## 藍綠部屬
1. 佈屬bookinfo含有bookinfo veviews-v1, veviews-v2 和veviews-v3
```bash
kubectl apply -f <(istioctl kube-inject -f samples/bookinfo/platform/kube/bookinfo.yaml)
```
2. 驗證 是否新增新的微服務
```bash
kubectl get deployments,ing
```
3. 應該看到類似的結果
```
NAME                                   DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
NAME                                   DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/details-v1       1         1         1            1           42m
deployment.extensions/productpage-v1   1         1         1            1           42m
deployment.extensions/ratings-v1       1         1         1            1           42m
deployment.extensions/reviews-v1       1         1         1            1           42m
deployment.extensions/reviews-v2       1         1         1            1           2m
deployment.extensions/reviews-v3       1         1         1            1           2m
4. 驗證 會看到有三個不同的星星顯示方式
```bash
echo http://$INGRESS_IP/productpage
```

