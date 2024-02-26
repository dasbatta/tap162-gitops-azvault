# Tanzu GitOps Reference Implementation

Use this archive contains an opinionated approach to implementing GitOps workflows on Kubernetes clusters.

This reference implementation is pre-configured to install Tanzu Application Platform.

For detailed documentation, refer to [VMware Tanzu Application Platform Product Documentation](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.5/tap/install-gitops-intro.html).

excluded_packages:
    - learningcenter.tanzu.vmware.com
    - workshops.learningcenter.tanzu.vmware.com
    - api-portal.tanzu.vmware.com
    - accelerator.apps.tanzu.vmware.com
    - apiserver.appliveview.tanzu.vmware.com
    - local-source-proxy.apps.tanzu.vmware.com
    - tap-gui.tanzu.vmware.com
    - crossplane.tanzu.vmware.com
    - bitnami.services.tanzu.vmware.com
cnrs:
    domain_template: '{{.Name}}-{{.Namespace}}.{{.Domain}}'
    default_tls_secret: tap-install/cluster-external-cert-tls-cert
    ingress_issuer: ""

excluded_packages:
    - crossplane.tanzu.vmware.com
    - bitnami.services.tanzu.vmware.com

    tap.fiservref1.net
    .fiservref2.net 

build:
    env:
    - name: BP_NODE_RUN_SCRIPTS
      value: build
    - name: NODE_ENV
      value: development
    - name: BP_NGINX_VERSION
      value: "1.32.3"
    - name: BP_WEB_SERVER
      value: nginx
    - name: BP_WEB_SERVER_ROOT
      value: dist
    - name: BP_WEB_SERVER_ENABLE_PUSH_STATE
      value: true


tanzu apps workload apply tanzu-java-web-app \
 --git-repo https://github.com/vmware-tanzu-learning/tanzu-java-web-app \
 --git-branch main \
 --label apps.tanzu.vmware.com/has-tests=true \
 --label app.kubernetes.io/part-of=tanzu-java-web-app \
 --type web \
 --namespace dev \
 --yes


---- 
Scan 2.0 - Gitops
tanzu package available get app-scanning.apps.tanzu.vmware.com/0.1.0-beta.137 --values-schema --namespace tap-install

----
Jan 2nd 2024, Pre-requisites to setup TAP on AKS for Fiserv 

- Executed Tanzu cluster essentials on both LOWERS clusters tapb & mt1

-   We need tls wildcard certs to be provisoned for both clusters 

**** Add the files (Secret and tlscertdeligation) under clusters/<clustername>/tanzu-sync/app/config/.tanzu-managed, 

- Create a tls secret cluster-external-cert-tls-cert in tap-install namespace 
- Create a tlscertificatedelegations for the wildcard cert 
- Update the tap-values >> cnrs to use the secret as a default certificate 

- Get the DNS entry for Contour IPs pointing to wildcard dns entry for both lower clusters

