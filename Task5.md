## Task  Delay & Fault Injection
##  bookinfo 延遲故障注入(Delay Injection) (1/4)

延遲故障注入，可以模擬當網路出現延遲時，微服務的運作是否正常

本段將模擬 reviews v3 到 ratings v1 的過程中出現網路延遲，健康的 bookinfo 服務時間如下圖

![delay-inject-before.png](imgs/delay-inject-before.png)

##  bookinfo 延遲故障注入(Delay Injection) (2/4)
部屬 veviews有三個版本以利後續演示
```bash
kubectl apply -f <(~/GKE-Istio/istio-1.0.5/bin/istioctl kube-inject -f  ~/GKE-Istio/$ISTIO_LAST/samples/bookinfo/platform/kube/bookinfo.yaml)
```
預設rule
```bash
kubectl apply -f ~/GKE-Istio/istio-1.0.5/samples/bookinfo/networking/destination-rule-all.yaml
```
```bash
kubectl apply -f ~/GKE-Istio/istio-1.0.5/samples/bookinfo/networking/virtual-service-ratings-test-delay.yaml
```
執行以下指令，將注入 1秒的延遲

```bash
kubectl apply -f ~/GKE-Istio/fault-inject-delay.yaml
```

設定細節說明
```
* __spec.http.fault.delay.fixedDelay__ : 注入延遲的時間
* __spec.http.fault.delay.percent__ : 注入錯誤的流量百分比 (0 ~ 100)
* __spec.http.match.sourceLabels.version__ : 流量來源的版號

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - fault:
      delay:
        fixedDelay: 1s
        percent: 100
    match:
    - sourceLabels:
        version: v3
    route:
    - destination:
        host: ratings
        subset: v1
  - route:
    - destination:
        host: ratings
        subset: v1
```

##  bookinfo 延遲故障注入(Delay Injection) (3/4)

延遲故障注入後，可以明顯看到，通過 `reviews v3` 的流量，回應時間明顯增加，如下圖

![delay-inject-after.png](imgs/delay-inject-after.png)

##  bookinfo 延遲故障注入 (Delay Injection)(4/4)

接著要展示， __中斷故障注入__ ，在開始之前，請執行以下指令，取消原本注入的錯誤

```bash
kubectl delete -f ~/GKE-Istio/fault-inject-delay.yaml
```

##  bookinfo 中斷故障注入(Fault Injection) (1/4)

中斷故障注入，可以模擬當其它服務異常時，微服務的運作是否正常

本段將模擬 reviews v3 到 ratings v1 的過程中出現中斷異常，健康的 bookinfo 服務時間如下圖

![inject-before.png](imgs/inject-before.png)

##  bookinfo 延遲故障注入(Fault Injection) (2/4)

執行以下指令，將注入 1秒的延遲

```bash
kubectl apply -f ~/GKE-Istio/fault-inject.yaml
```

設定細節說明
```
* __spec.http.fault.delay.fixedDelay__ : 注入延遲的時間
* __spec.http.fault.delay.percent__ : 注入錯誤的流量百分比 (0 ~ 100)
* __spec.http.match.sourceLabels.version__ : 流量來源的版號
```

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - fault:
      abort:
        httpStatus: 500
        percent: 100
    match:
    - sourceLabels:
        version: v3
    route:
    - destination:
        host: ratings
        subset: v1
  - route:
    - destination:
        host: ratings
        subset: v1
```

##  bookinfo 中斷故障注入(Fault Injection) (3/4)

中斷故障注入後，可以明顯看到， `reviews v3` 往 `ratings v1` 的流量，全部都收到 `http: 500` 的中斷

![inject-after.png](imgs/inject-after.png)

##  bookinfo 中斷故障注入(Fault Injection) (4/4)

Cleanup

```bash
kubectl delete -f ~/GKE-Istio/fault-inject.yaml
```
