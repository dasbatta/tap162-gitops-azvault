### **🟦 Knative (Kubernetes-native serverless platform)**

**Knative** simplifies deploying and scaling applications on Kubernetes, especially for stateless microservices or event-driven workloads.  

### 🚀 **Key Features:**  
✅ **Autoscaling** — Automatically scales your pods to zero when idle and scales up based on demand.  
✅ **Traffic Splitting** — Handles blue-green deployments, canary releases, and A/B testing.  
✅ **Event-Driven** — Uses event sources to trigger workloads in a serverless fashion.  

### 🏗️ **How it Fits In:**  
Imagine you have a web app that sees minimal traffic at night. Knative scales the pods down to zero and only spins them back up when requests arrive — saving resources and costs.

---

### **🟩 KFServing (Now KServe)**
**KFServing** (now called **KServe**) is an **AI/ML model serving framework** designed for Kubernetes. It simplifies deploying machine learning models by abstracting complex Kubernetes configurations.  

### 🚀 **Key Features:**  
✅ **Model Versioning** — Easily manage multiple model versions.  
✅ **Autoscaling (with Knative)** — Scales model inference pods up/down based on request load.  
✅ **Predictive Routing** — Routes requests intelligently between model versions for canary testing.  
✅ **Multi-framework Support** — Supports **TensorFlow**, **PyTorch**, **XGBoost**, **Triton**, etc.  

### 🏗️ **How it Fits In:**  
Suppose you have a computer vision model in TensorFlow. Instead of writing complex Kubernetes YAML for inference, you define a simple `InferenceService` CRD in KServe. It handles scaling, request routing, and model versioning for you.  

---

### **🔥 How They Work Together**
- **Knative** provides the **serverless** capabilities like autoscaling and request routing.  
- **KServe** (previously KFServing) leverages Knative to manage **AI/ML inference** workloads efficiently.  

### **Example Workflow:**  
1. Deploy your AI model with **KServe**.  
2. KServe uses **Knative** to automatically scale pods based on traffic.  
3. When no inference requests are active, Knative scales pods to **zero** to save resources.  

---

### **Analogy:**  
- **Knative** = A smart thermostat that adjusts your home’s heating automatically based on temperature.  
- **KServe** = A specialized thermostat designed specifically for AI model environments.  

----

Here's a simple example of **auto-scaling in Knative** using a basic Python Flask app deployed on Kubernetes.

---

## **Step 1: Create a Python Flask App**
We'll create a minimal Flask app that responds with `"Hello, Knative!"`.

### **app.py**
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Knative!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8080)
```

---

## **Step 2: Create a Dockerfile**
We'll containerize the app.

### **Dockerfile**
```dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY app.py .
RUN pip install flask
CMD ["python", "app.py"]
```

Build and push the image:  
```bash
docker build -t <your-dockerhub-username>/knative-flask-app:latest .
docker push <your-dockerhub-username>/knative-flask-app:latest
```

---

## **Step 3: Create a Knative Service**
Knative uses a `Service` CRD to manage autoscaling.

### **knative-service.yaml**
```yaml
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: knative-flask-app
spec:
  template:
    spec:
      containers:
        - image: <your-dockerhub-username>/knative-flask-app:latest
          resources:
            limits:
              cpu: "500m"
              memory: "256Mi"
  # Autoscaling Settings
  annotations:
    autoscaling.knative.dev/minScale: "1"   # Minimum 1 pod always running
    autoscaling.knative.dev/maxScale: "5"   # Maximum 5 pods
    autoscaling.knative.dev/target: "10"    # Target concurrency per pod
```

---

## **Step 4: Deploy the Service**
Run these commands to deploy:

```bash
kubectl apply -f knative-service.yaml
```

---

## **Step 5: Send Traffic to Trigger Autoscaling**
Knative will automatically scale pods based on incoming requests.

### Generate Traffic:
```bash
watch -n 1 curl $(kubectl get ksvc knative-flask-app -o jsonpath='{.status.url}')
```

### Check Pod Scaling:
```bash
kubectl get pods
```

- As traffic increases, Knative will **scale up** pods.  
- If traffic goes idle, Knative will **scale down to zero** (if configured).

---

## **Step 6: Monitoring Autoscaling**
To monitor Knative metrics:  
```bash
kubectl get pods -w
kubectl get ksvc knative-flask-app
```

---

## **Key Knative Autoscaling Annotations**
| Annotation | Description |
|:------------|:------------|
| `autoscaling.knative.dev/minScale` | Minimum number of pods. |
| `autoscaling.knative.dev/maxScale` | Maximum number of pods. |
| `autoscaling.knative.dev/target`   | Target concurrency per pod (requests per pod before scaling). |
| `autoscaling.knative.dev/scaleDownDelay` | Delay before scaling down (default: 30s). |

---
