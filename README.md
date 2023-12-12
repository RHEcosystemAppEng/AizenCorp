# AizenCorp (RAY operator + Spark Operator + ML Flow)

An End-to-End AI stack that collapses all layers of AI and automatically transforms ​real-time operational data into AI predictions

* Easy: Deploys to on-premise orprivate cloud infrastructure in minutes
* Automatic: Generates AI models for real-time business data analysis
* Low Code: Suitable for data analysts and data scientists at any level of AI proficiency
* Scalable: Easily scales from prototyping to production clusters

## Objective

Deploy AizenCorp on Red Hat OpenShift Data Science platform.

## Deployment

### Pre-requisites

* Kubernetes Cluster
  
### Components in K8s cluster

* Ray Operator
* Container Storage Provider, Storage Class (Persistent Volume)
* Spark Operator
  
### Monitoring & Alerting

* Prometheus & Grafana
  
### S3 compatible Object store

* Red Hat OpenShift Container Storage
* AWS S3
* MinIO
  
### MLFlow

## BOM

| Software                                        | Component Version | Description / Function                           | Vendor  | Component Implementer | Number of Instances | Total Storage Requirements in GB | Total RAM in GB | Remarks/Notes |
|-------------------------------------------------|-------------------|--------------------------------------------------|---------|-----------------------|---------------------|----------------------------------|-----------------|---------------|
| Openshift Master                                | 4.12+             | Control Plane                                    | Red Hat | Red Hat               | 3 (4 core)          | 120                              | 16              | m6i.xlarge    |
| Openshift Worker                                | 4.12+             | Data Plane                                       | Red Hat | Red Hat               | 3 (16 core)         | 120                              | 64              | c6i.4xlarge   |
| Bastion Node                                    | RHEL              |                                                  | Red Hat | Red Hat               |                     |                                  |                 |               |
| Spark Operator                                  | 3.1.1             | Analytics engine for large-scale data processing | Apache  | Red Hat               |                     |                                  |                 |               |
| Ray Operator                                    | 2.8.0             | Scaling of AI / ML workloads                     | NA      | Red Hat               |                     |                                  |                 |               |
| Prometheus                                      | 4.10.0            | Monitoring and Alerting                          | NA      | Red Hat               |                     |                                  |                 |               |
| Grafana                                         |                   | Visualization / Dashboard                        |         |                       |                     |                                  |                 |               |
| S3 Compatible Object Store                      |                   |                                                  |         |                       |                     |                                  |                 |               |
| &nbsp;- AWS S3                              |                   | Data Store - S3 Compatible                       | AWS     | AWS                   |                     |                                  |                 |               |
| &nbsp;- MinIO                               |                   | Data Store - S3 Compatible                       | Minio   | Minio                 |                     |                                  |                 |               |
| &nbsp;- Red Hat OpenShift Container Storage | 4.12.9            | Data Store - S3 Compatible                       | Red Hat | Red Hat               | 3 (16 core)         | 120                              | 64              | c6i.4xlarge   |
| MLFlow                                          |

## Installation

### Ray Cluster

Instructions to deploy [Ray Cluster](https://docs.ray.io/en/latest/cluster/kubernetes/getting-started/raycluster-quick-start.html#kuberay-raycluster-quickstart).

1. Add helm repository

   ```sh
   helm repo add kuberay https://ray-project.github.io/kuberay-helm/
   helm repo update
   ```

2. Create namespace

   ```sh
   oc new-project ray-odh
   ```

3. Install kuberay-operator

   ```sh
   helm install kuberay-operator kuberay/kuberay-operator --version 0.4.0
   ```

4. Install Ray cluster

   ```sh
   helm install raycluster kuberay/ray-cluster --version 0.4.0
   ```

5. Install kuberay-apiserver

   ```sh
   helm install kuberay-apiserver kuberay/kuberay-apiserver --version 0.4.0
   ```

### Spark Operator

Instructions to deploy [Spark Operator](https://github.com/opendatahub-io-contrib/spark-on-openshift/).

#### Prerequisites

* OCP cluster with ODF

Steps:

(1 to 5 are the steps for the history server deployment. 6 onwards is spark operator deployment)

1. Clone Git repository

   ```sh
   git clone git@github.com:opendatahub-io-contrib/spark-on-openshift.git
   cd spark-on-openshift
   ```

2. Deploy Spark History Server

   To avoid losing this precious information, you can (and you should!) send all the logs to a specific location and set up the Spark History Server to be able to view and interpret them at any time.

   Follow the [instructions](https://github.com/opendatahub-io-contrib/spark-on-openshift#spark-history-server) to deploy Spark History Server.

   You can use OpenShift Data Foundation in which case you will have to do the following steps before creating the ObjectBucketClaim.

   a. [Prepare](https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/4.9/html/deploying_openshift_data_foundation_using_ibm_z_infrastructure/preparing_to_deploy_openshift_data_foundation) to deploy OpenShift Data Foundation.
   b. [Deploying](https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/4.9/html/deploying_and_managing_openshift_data_foundation_using_red_hat_openstack_platform/deploying_openshift_data_foundation_on_red_hat_openstack_platform_in_internal_mode) OpenShift Data Foundation on Red Hat OpenStack Platform in internal mode

3. Deploy Spark Operator

   Follow the [instructions](https://github.com/opendatahub-io-contrib/spark-on-openshift/tree/main#working-with-the-spark-operator) to install the standalone spark operator.

### Prometheus Server

Instructions to deploy Prometheus Server

#### Install Prometheus Operator

1. Select the project from the Project list where you want to install the Prometheus operator.
2. In the left panel, go to Operators > OperatorHub.
3. To find the Prometheus operator, enter the search term “prometheus”, and click Prometheus Operator provided by Red Hat.
4. On the Prometheus Operator page click Install. The Create Operator Subscription page is shown. Complete the following steps to create the Prometheus operator subscription:

   a. Select the installation mode by clicking A specific namespace on the cluster, and choose the project where you want to install the Prometheus operator.

   b. If the Prometheus operator is available through various channels, choose the appropriate channel that you want to subscribe to.

   c. Click stable to deploy from the stable channel. Select the Approval Strategy as Manual.

   d. Click Subscribe. The Prometheus operator details are shown on the Installed Operator page. The status is shown as “UpgradePending”.

   e. Click the Prometheus operator from the Name column, and click 1 requires approval link.

   f. Click Preview Install Plan. The details about the manual installation plan are shown.

   g. Click Approve.

#### Creating Prometheus instance

1. Log in to the OpenShift Container Platform (OCP) by using the OpenShift administrator credentials.
2. In the left panel, go to Operators > Installed Operators.
3. Select the project from the Project list, where you installed Prometheus operator.
4. Click the Prometheus Operator link from the Name column.
5. Click the Prometheus tab, and click Create Prometheus.
6. On the Create Prometheus page, use one of the following options to edit the custom resource to create a Prometheus instance:
   * YAML View: You are provided with full control of object creation.
   * Form View: You can enter the details in a form.
  
7. Enter the following details in the YAML file:
   * name: Enter the name of the Prometheus instance. Refer to the example, wherein the Prometheus instance name is entered as app-monitor.
   * namespace: Enter the name of the project or the namespace where you installed Prometheus operator. Refer to the example, wherein the namespace is entered as prometheus.
   * As shown in the example, enter the following details in the Custom Resource (CR). Enter the key that is mentioned is in the service monitors that points to RQA services. Refer to the example, wherein the Key is mentioned as aizen-app.
  
      ```
      serviceMonitorSelector:
         matchExpressions:
            - key: aizen-app
            operator: Exists
      ```

      Example:

      ```
      apiVersion: monitoring.coreos.com/v1
      kind: Prometheus
      metadata:
         name: app-monitor
      labels:
         prometheus: k8s
      namespace: prometheus
      spec:
      replicas: 1
      serviceAccountName: prometheus-k8s
      securityContext: {}
      serviceMonitorSelector:
         matchExpressions:
            - key: aizen-app
            operator: Exists
      ruleSelector: {}
      alerting:
         alertmanagers:
            - namespace: openshift-monitoring
            name: alertmanager-main
            port: web
      ```

8. Click Create.
9. Adding a cluster role to the user.

   Provide the view cluster role to the Service Account that is created by the Prometheus operator.
   Run the following command:

   ```
   oc adm policy add-cluster-role-to-user view system:serviceaccount:<project name>:prometheus-k8s
   ```

### Grafana

Install the Grafana operator in the same project or the namespace where you installed the Prometheus operator.

#### Install Grafana Operator

1. Log in to the OpenShift Container Platform (OCP) by using the OpenShift administrator credentials.
2. Select the project from the Project list where you installed the Prometheus operator.
3. In the left panel, go to Operators > OperatorHub.
4. To find the Grafana operator, enter the search term “grafana”, and click Grafana Operator provided by Red Hat, which is a community operator.
5. On the Grafana Operator page click Install. The Create Operator Subscription page is shown. Complete the following steps to create the Grafana operator subscription:
   * Select the installation mode by clicking A specific namespace on the cluster, and choose your project where you want to install the Grafana operator.
   * If the Grafana operator is available through various channels, choose the appropriate channel that you want to subscribe to. Click stable to deploy from the stable channel.
   * Select the Approval Strategy as Manual.
   * Click Subscribe. The Grafana operator details are shown on the Installed Operator page. The status is shown as “UpgradePending”.
   * Click the Grafana operator from the Name column, and click 1 requires approval link.
   * Click Preview Install Plan. The details about the manual installation plan are shown.
   * Click Approve.

#### Create Grafana Instance

1. In the left panel, go to Operators > Installed Operators. Select the project where you installed Grafana operator.
2. Click the Grafana Operator link from the Name column.
3. Click the Grafana tab, and click Create Grafana.
4. On the Create Grafana page, use one of the following options to edit the custom resource to create a Grafana instance:
   * YAML View: You are provided with full control of object creation.
   * Form View: You can enter the details in a form.
5. Enter the following details in the YAML file:
   * name: Enter the name of the Grafana instance. Refer to the example, wherein the Grafana instance name is entered as example-grafana.
   * namespace: Enter the name of the project or the namespace where you installed Grafana operator. Refer to the example, wherein the Grafana namespace name is entered as prometheus.
   * admin_user: Enter the username. Refer to the example, wherein the username is provided as root.
   * admin_password: Enter the password. Refer to the example, wherein the username is provided as secret
  
   Example:

   ```
   apiVersion: integreatly.org/v1alpha1
   kind: Grafana
   metadata:
   name: example-grafana
   namespace: prometheus
   spec:
   ingress:
      enabled: true
   config:
      auth:
         disable_signout_menu: true
      auth.anonymous:
         enabled: true
      log:
         level: warn
         mode: console
      security:
         admin_password: secret
         admin_user: root
   dashboardLabelSelector:
      - matchExpressions:
         - key: app
            operator: In
            values:
               - grafana
   ```

6. Click Create.

#### Create Grafana route

Run the following command:

```
oc expose svc/grafana-route -n <project name>
```

#### Create Grafana DataSource

1. Log in to the OpenShift Container Platform (OCP) by using the OpenShift administrator credentials.
2. In the left panel, go to Operators > Installed Operators. Select the project where you installed Grafana operator.
3. Click the Grafana Operator link from the Name column, and click the Grafana Data Source tab.
4. Click Create GrafanaDataSource.
5. Enter the following details in the YAML file:
   * name: Enter the name of the Grafana Data Source. Refer to the example, wherein the Grafana Data Source name is entered as example-grafanadatasource.
   * namespace: Enter the name of the project or the namespace where you installed Grafana operator. Refer to the example, wherein the Grafana namespace name is entered as prometheud.
   * url: Enter the Prometheus service URL.
  
   Example:

   ```
   apiVersion: integreatly.org/v1alpha1
   kind: GrafanaDataSource
   metadata:
   name: example-grafanadatasource
   namespace: prometheus
   spec:
   datasources:
      - access: proxy
         editable: true
         isDefault: true
         jsonData:
         timeInterval: 5s
         name: Prometheus
         type: prometheus
         url: <Prometheus service URL>
         version: 1
   name: example-datasources.yaml
   ```

### MinIO

MinIO is an object storage solution that provides an Amazon Web Services S3-compatible API and supports all core S3 features. MinIO is built to deploy anywhere - public or private cloud, baremetal infrastructure, orchestrated environments, and edge infrastructure.

Follow the [instructions](https://min.io/docs/minio/kubernetes/openshift/operations/installation.html) to Deploy MinIO

### MLFlow Server

MLflow is an open source platform to manage the ML lifecycle, including experimentation, reproducibility, deployment, and a central model registry.

Helm installation into OpenShift namespace

#### Pre-requisites

* Install the "Crunchy Postgres for Kubernetes" operator (can be found in OperatorHub) - To store the MLFlow config
* Install the "OpenShift Data Foundation" operator (can be found in OperatorHub) - To provide S3 storage for the experiments and models.
  As an alternative use Amazon S3 or MinIO.

#### Install

```
<Create an OpenShift project, either through the OpenShift UI or 'oc new-project project-name'>
helm repo add strangiato https://strangiato.github.io/helm-charts/
helm repo update
<Log in to the correct OpenShift project through 'oc project project-name'>
helm upgrade -i mlflow-server strangiato/mlflow-server
```

#### Additional Options

The MLFlow Server helm chart provides a number of customizable options when deploying MLFlow. These options can be configured using the --set flag with helm install or helm upgrade to set options directly on the command line or through a values.yaml file using the --values flag.

For a full list of configurable options, see the helm chart [documentation](https://github.com/strangiato/helm-charts/tree/main/charts/mlflow-server#values)

OPENDATAHUB DASHBOARD APPLICATION TILE
As discussed in the Dashboard Configuration, ODH/RHODS allows administrators to add a custom application tile for additional components on the cluster.

The MLFlow Server helm chart supports creation of the Dashboard Application tile as a configurable value. If MLFlow Server is installed in the same namespace as ODH/RHODS you can install the dashboard tile run the following command:

```
helm upgrade -i mlflow-server strangiato/mlflow-server \
    --set odhApplication.enabled=true
```

The MLFlow Server helm chart also supports installing the odhApplication object in a different namespace, if MLFlow Server is not installed in the same namespace as ODH/RHODS:

```
helm upgrade -i mlflow-server strangiato/mlflow-server \
    --set odhApplication.enabled=true \
    --set odhApplication.namespaceOverride=redhat-ods-applications
After enabling the odhApplication component, wait 1-2 minutes and the tile should appear in the Explorer view of the dashboard.
```

Note: This feature requires ODH v1.4.1 or newer

#### Test MLFlow

1. Go to the OpenShift Console and switch to Developer view.
2. Go to the Topology view and make sure that you are on the MLFlow project.
3. Check that the MLFlow circle is dark blue (this means it has finished deploying).
4. Press the "External URL" link in the top right corner of the MLFlow circle to open up the MLFlow UI.
5. Run helm test mlflow-server in your command prompt to test MLFlow. If successful, you should see a new experiment called "helm-test" show up in the MLFlow UI with 3 experiments inside it.
