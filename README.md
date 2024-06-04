# istio
istio service mesh demo
These steps require you to have a cluster running a supported version of Kubernetes (1.27, 1.28, 1.29, 1.30). 

## Follow these steps to get started with Istio:
- Download and install Istio
- Deploy the sample application
- Open the application to outside traffic
- View the dashboard

## 1. Download Istio
- curl -L https://istio.io/downloadIstio | sh -
- cd istio-1.22.0
## Add the istioctl client to your path
- export PATH=$PWD/bin:$PATH

## 2. Install Istio
 For this installation, we use the demo configuration profile. It’s selected to have a good set of defaults for testing, but there are other profiles for production or performance testing.
- istioctl install --set profile=demo -y
 Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later
- kubectl label namespace default istio-injection=enabled
  
## 3. Deploy the sample application
Deploy the Bookinfo sample application
- kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
The application will start. As each pod becomes ready, the Istio sidecar will be deployed along with it.
- kubectl get svc
- kubectl get pods
Verify everything is working correctly up to this point
- kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"


