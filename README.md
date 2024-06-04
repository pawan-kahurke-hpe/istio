# istio
istio service mesh demo
These steps require you to have a cluster running a supported version of Kubernetes (1.27, 1.28, 1.29, 1.30). 

## Follow these steps to get started with Istio:
- Download and install Istio
- Deploy the sample application
- Open the application to outside traffic
- View the dashboard

## 1.1 Download Istio
```python curl -L https://istio.io/downloadIstio | sh -
```
- cd istio-1.22.0
## Add the istioctl client to your path
- export PATH=$PWD/bin:$PATH

## 1.2 Install Istio
 For this installation, we use the demo configuration profile. It’s selected to have a good set of defaults for testing, but there are other profiles for production or performance testing.
- istioctl install --set profile=demo -y
 Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later
- kubectl label namespace default istio-injection=enabled
  
## 2. Deploy the sample application
Deploy the Bookinfo sample application
- kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
  
The application will start. As each pod becomes ready, the Istio sidecar will be deployed along with it.

- kubectl get svc
- kubectl get pods

Verify everything is working correctly up to this point
- kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"

## 3. Open the application to outside traffic
The Bookinfo application is deployed but not accessible from the outside. To make it accessible, you need to create an Istio Ingress Gateway, which maps a path to a route at the edge of your mesh.

1. Associate this application with the Istio gateway
- kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
2. Ensure that there are no issues with the configuration
- istioctl analyze
3. Execute the following command to determine if your Kubernetes cluster is running in an environment that supports external load balancers
- export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
- export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
- export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')

4. Set GATEWAY_URL:
- export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

5. Ensure an IP address and port were successfully assigned to the environment variable:
- echo "$GATEWAY_URL"
## Verify external access
Confirm that the Bookinfo application is accessible from outside by viewing the Bookinfo product page using a browser.
Run the following command to retrieve the external address of the Bookinfo application.

- ''' echo "http://$GATEWAY_URL/productpage" '''
  
Paste the output from the previous command into your web browser and confirm that the Bookinfo product page is displayed.

## View Dashboard
istio integrates with several different telemetry applications. 
These can help you gain an understanding of the structure of your service mesh, display the topology of the mesh, and analyze the health of your mesh.

Use the following instructions to deploy the Kiali dashboard, along with Prometheus, Grafana, and Jaeger.

Install Kiali and the other addons and wait for them to be deployed.
kubectl apply -f samples/addons
kubectl rollout status deployment/kiali -n istio-system

Access the Kiali dashboard.
istioctl dashboard kiali







