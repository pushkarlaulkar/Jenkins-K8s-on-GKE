Instructions to deploy **Jenkins** on GCP GKE Auto Pilot cluster
  1. Deploy GKE Auto Pilot cluster GCP Console. **NAT Gateway** is required to download plugins.
  2. Create a namespace. ` kubectl create ns jenkins `
  3. Deploy **PVC** & **Storage Class** using below command.

     ```
     kubectl -n jenkins apply -f pvc.yml -f storage-class.yml
     ```
  4. Deploy the **Jenkins** deployment & service using the below command

     ```
     kubectl -n jenkins apply -f jenkins-dep.yml -f jenkins-svc.yml
     ```
  5. Deploy **Ingress**, **Managed Certificate** & **Frontend Config** which will create an ALB listening on port 443 by running below command. Before running below command, in the **Managed Certificate** put the domain name you need for your application.
     ```
     kubectl -n jenkins apply -f ingress-all.yml
     ```
  6. Run ` kubectl -n jenkins get ingress ` to retrieve the ALB IP ( Please wait couple of minutes ). Create an A record in **Cloud DNS** or your own DNS service pointing your domain name to this IP.
  7. Once the DNS entry has been added it will take couple of minutes ( can be 60 minutes in some cases ) for the certificate to be generated. Run ` kubectl -n jenkins get managedcertificate ` or in GCP console to check for **Active** status.
  8. Access the app using `https://your_domain_name`.

-----------------------------

**Helm**
To install this app using Helm, perform below steps
  1. Run the command

     ```
     helm install jenkins ./helm --namespace jenkins --create-namespace --set domain_name=domain_name
     ```
  2. Get the ALB IP using ` kubectl -n jenkins get ingress ` ( Please wait couple of minutes ). Create an A record in **Cloud DNS** or your own DNS service pointing your domain name to this IP.
  3. Once the DNS entry has been added it will take couple of minutes ( can be 60 minutes in some cases ) for the certificate to be generated. Run ` kubectl -n jenkins get managedcertificate ` or in GCP console to check for **Active** status.
  4. Access the app using ` https://your_domain_name `.
  5. Uninstall the app using ` helm uninstall jenkins --namespace jenkins `.

-----------------------------

**ArgoCD** installation
To install this app using ArgoCD, perform below steps
  1. Create a namespace. ` kubectl create ns argocd `.
  2. Apply ArgoCD manifest file
     
     ```
     kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
     ```
  3. Deploy **ArgoCD Ingress**, **ArgoCD Managed Certificate** & **ArgoCD Frontend Config** which will create an ALB listening on port 443 by running below command. Before running below command, in the **ArgoCD Managed Certificate** put the domain name you need for your ArgoCD application.

     ```
     kubectl -n argocd apply -f argocd-ingress.yml
     ```
  4. Run ` kubectl -n argocd get ingress ` to retrieve the ALB IP ( Please wait couple of minutes ). Create an A record in **Cloud DNS** or your own DNS service pointing your ArgoCD domain name to this IP.
  5. Once the DNS entry has been added it will take couple of minutes ( can be 60 minutes in some cases ) for the certificate to be generated. Run ` kubectl -n argocd get managedcertificate ` or in GCP console to check for **Active** status.
  6. Access ArgoCD using ` https://argocd_domain_name `.
  7. To get the initial admin user password run the command

     ```
     kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode
     ```