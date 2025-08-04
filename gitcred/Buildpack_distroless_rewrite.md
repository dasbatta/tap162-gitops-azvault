Demo: Tanzu Buildpack Rebuilding with Google Distroless for Docker multi-stage builds

Rebuilding a Tanzu Buildpack while incorporating Google Distroless images as the base can improve security and reduce attack surfaces. Below is a structured approach to achieving this while setting an example for best practices.

---

### **1. Understanding the Need for Distroless**
Google's [Distroless images](https://github.com/GoogleContainerTools/distroless) eliminate unnecessary OS components, reducing vulnerabilities and making containers more secure. Tanzu Buildpacks typically use Ubuntu or other minimal OS images, but switching to Distroless can further harden the environment.

---

### **2. Cloning and Modifying the Tanzu Buildpack**
#### **Step 1: Clone the Tanzu Buildpack Repository**
```bash
git clone https://github.com/paketo-buildpacks/go
cd go
```
(Replace `go` with the relevant buildpack you want to modify.)

#### **Step 2: Update `builder.toml` to Use a Distroless Base**
Modify the `builder.toml` to use a Google Distroless image:

```toml
[[buildpacks]]
  uri = "docker://gcr.io/paketo-buildpacks/go"

[build]
  [[build.images]]
    image = "gcr.io/distroless/static:nonroot"
```
Here, we're switching the base layer to `gcr.io/distroless/static:nonroot`.

#### **Step 3: Update the Lifecycle for Compatibility**
Ensure the `lifecycle.toml` and buildpack stack definitions support Distroless by modifying `stack.toml`:
```toml
[build]
  image = "gcr.io/distroless/base"
  run-image = "gcr.io/distroless/static"
```

---

### **3. Rebuilding the Buildpack**
#### **Step 1: Build the Custom Buildpack**
```bash
pack buildpack package my-custom-buildpack --path . --format image
```

#### **Step 2: Create a Builder with the New Base Image**
```bash
pack builder create my-custom-builder --config builder.toml
```

#### **Step 3: Build an App Using the Custom Buildpack**
```bash
pack build my-app --builder my-custom-builder
```

---

### **4. Validating and Testing**
After rebuilding the buildpack, validate the app:
- Ensure it runs as a non-root user:
  ```bash
  docker run --rm -it --user 1000:1000 my-app
  ```
- Scan the image for vulnerabilities:
  ```bash
  trivy image my-app
  ```

---

### **5. Conclusion**

✅ Improve security by reducing attack surfaces from the get-go.
✅ Promote best practices with non-root execution.  
✅ Ensure compatibility with modern container security standards.  

This approach exemplifies **secure, efficient, and maintainable** containerization for Tanzu Buildpacks.

*** Quick demo of build pack re-packaging ***
