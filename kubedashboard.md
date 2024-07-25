To install and access the Kubernetes Dashboard on your local Kubernetes cluster set up through Docker Desktop, follow these steps:

### Step 1: Deploy the Kubernetes Dashboard
1. **Deploy the Dashboard using `kubectl`**:
   Open a terminal and execute the following command to deploy the Kubernetes Dashboard:
   ```sh
   kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
   ```

### Step 2: Create a Service Account
1. **Create a service account and cluster role binding**:
   To access the Dashboard, you need to create a service account with appropriate permissions. Run the following commands:
   ```sh
   kubectl create serviceaccount dashboard-admin-sa
   kubectl create clusterrolebinding dashboard-admin-sa --clusterrole=cluster-admin --serviceaccount=default:dashboard-admin-sa
   ```

### Step 3: Obtain the Bearer Token
1. **Get the Bearer token for authentication**:
   Retrieve the token to log into the Dashboard with:
   ```sh
   kubectl get secrets
   ```
   Look for a secret associated with `dashboard-admin-sa`. It should look something like `dashboard-admin-sa-token-xxxxx`.

2. **Describe the secret to get the token**:
   ```sh
   kubectl describe secret dashboard-admin-sa-token-xxxxx
   ```
   Copy the token under the `token:` field.

### Step 4: Access the Dashboard
1. **Start the `kubectl` proxy**:
   Run the following command to start the proxy:
   ```sh
   kubectl proxy
   ```

2. **Access the Dashboard**:
   Open a web browser and navigate to:
   ```
   http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
   ```

3. **Login to the Dashboard**:
   Use the token you obtained in Step 3 to log in.

### Summary of Commands
Here's a summary of the commands you'll run in sequence:
```sh
# Deploy Kubernetes Dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

# Create Service Account and Cluster Role Binding
kubectl create serviceaccount dashboard-admin-sa
kubectl create clusterrolebinding dashboard-admin-sa --clusterrole=cluster-admin --serviceaccount=default:dashboard-admin-sa

# Get the Bearer Token
kubectl get secrets
# Find the secret name and then describe it
kubectl describe secret dashboard-admin-sa-token-xxxxx

# Start kubectl proxy
kubectl proxy
```

By following these steps, you should be able to install and access the Kubernetes Dashboard on your local cluster set up with Docker Desktop


If `kubectl get secrets` is not listing the `dashboard-admin-sa-token-xxxx` secret, it might be because the secret hasn't been created yet. Sometimes, it might take a moment for Kubernetes to create the token secret for the service account. Here are a few steps to troubleshoot and ensure you get the token:

### Step 1: Verify the Service Account
Ensure that the service account `dashboard-admin-sa` was created successfully:
```sh
kubectl get serviceaccounts
```
You should see `dashboard-admin-sa` listed in the output.

### Step 2: Verify the Cluster Role Binding
Ensure that the cluster role binding is in place:
```sh
kubectl get clusterrolebindings
```
Look for `dashboard-admin-sa` in the list.

### Step 3: Manually Create the Secret (if necessary)
If the token secret still isn't appearing, you can manually create it. Here's how:

1. **Create a YAML file for the token secret**:
   Create a file named `dashboard-admin-sa-secret.yaml` with the following content:
   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: dashboard-admin-sa-token
     annotations:
       kubernetes.io/service-account.name: dashboard-admin-sa
   type: kubernetes.io/service-account-token
   ```

2. **Apply the YAML file**:
   ```sh
   kubectl apply -f dashboard-admin-sa-secret.yaml
   ```

### Step 4: Retrieve the Token
After creating the secret manually, retrieve the token as follows:

1. **Get the list of secrets again**:
   ```sh
   kubectl get secrets
   ```
   Now you should see `dashboard-admin-sa-token`.

2. **Describe the secret to get the token**:
   ```sh
   kubectl describe secret dashboard-admin-sa-token
   ```
   Copy the token under the `token:` field.

### Summary of Commands
Here's a summary of additional commands for troubleshooting:

```sh
# Verify the service account
kubectl get serviceaccounts

# Verify the cluster role binding
kubectl get clusterrolebindings

# If necessary, manually create the secret
# Create a YAML file and apply it
kubectl apply -f dashboard-admin-sa-secret.yaml

# Retrieve the token
kubectl get secrets
kubectl describe secret dashboard-admin-sa-token
```

By following these steps, you should be able to retrieve the token required to log in to the Kubernetes Dashboard.


eyJhbGciOiJSUzI1NiIsImtpZCI6InRyVFN3LWRRRmxUYVFwUW9LUmxOdUNiTkFaNG5YalNYZ2c5V1FseHZFRzAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRhc2hib2FyZC1hZG1pbi1zYS10b2tlbiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJkYXNoYm9hcmQtYWRtaW4tc2EiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJlZWIyZWQwNS1kNmI3LTQ0NTMtOWE4NS0yYjNjMjA1NzZiMGQiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpkYXNoYm9hcmQtYWRtaW4tc2EifQ.uxwW1KL9PTOpgWCwI0IZYRmRrCH-wTC5eMWhdJF8s7d0mdbhOAZ7_hgJo_u815iMDnKfjJ8gnQUesebKOM2MWPCnTLHH_PezjUI8tycYBT5t_dp352anaV0gEgj9wtsNCTrWsSQZR9mYXh2u_0AMSx8_X839DklNgANHGfdDMShLYpY0ivvIi9BMAYE4VDwhe_-W4u2IwGk1mG_dH_hTJr_CZBRvN9fhWbhW4d0PLNm73F0lA7CEjymGIMt-xKIVF6FxI2DbA1joLJPxbhW1iMH2tT9JM0C6dv72IUJK0v8VeaPcCqChzf4SjAH95llRyhRxT1JtdgkVaNia7KXN9g