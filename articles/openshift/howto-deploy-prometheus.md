---
title: 在 Azure Red Hat OpenShift 叢集中部署 Prometheus 實例
description: 在 Azure Red Hat OpenShift 叢集中建立 Prometheus 實例，以監視應用程式的計量。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus、aro、openshift、計量、red hat
ms.openlocfilehash: 7f22df587f51af735e0ea663e53f6eef14d60692
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80886883"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 叢集中部署獨立的 Prometheus 實例

本文說明如何設定在 Azure Red Hat OpenShift 叢集中使用服務探索的獨立 Prometheus 實例。

> [!NOTE]
> 不需要客戶系統管理員對 Azure Red Hat OpenShift 叢集的存取權。

目標設定：

- 一個專案（prometheus-project），其中包含 Prometheus 和 Alertmanager。
- 兩個專案（應用程式 project1 和應用程式 project2），其中包含要監視的應用程式。

您將會在本機準備一些 Prometheus 設定檔案。 建立新的資料夾來儲存它們。 設定檔會以秘密的形式儲存在叢集中，以防秘密權杖稍後新增至叢集。

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>使用 OC 工具登入叢集

1. 開啟網頁瀏覽器，然後移至叢集的 web 主控台（ https://openshift 。*隨機識別碼*。*region*. azmosa.io）。
2. 利用您的 Azure 認證登入。
3. 在右上角選取您的使用者名稱，然後選取 [**複製登入命令**]。
4. 將您的使用者名稱貼入您將使用的終端機。

> [!NOTE]
> 若要查看您是否已登入正確的叢集，請執行 `oc whoami -c` 命令。

## <a name="prepare-the-projects"></a>準備專案

若要建立專案，請執行下列命令：
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 您可以使用 `-n` 或 `--namespace` 參數，或藉由執行命令來選取作用中的專案 `oc project` 。

## <a name="prepare-the-prometheus-configuration-file"></a>準備 Prometheus 設定檔
輸入下列內容來建立 prometheus yml 檔案：
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
輸入下列設定，以建立名為 Prom 的秘密：
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus. yml 檔案是基本的 Prometheus 設定檔。 它會設定間隔，並在三個專案（prometheus-專案、應用程式 project1、應用程式 project2）中設定自動探索。 在先前的設定檔中，自動探索的端點會透過 HTTP 剪輯，而不會進行驗證。

如需抓取端點的詳細資訊，請參閱[Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)。


## <a name="prepare-the-alertmanager-config-file"></a>準備 Alertmanager 設定檔
輸入下列內容來建立 alertmanager yml 檔案：
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
輸入下列設定來建立名為 [Prom-警示] 的秘密：
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. yml 是警示管理員設定檔。

> [!NOTE]
> 若要確認先前的兩個步驟，請執行 `oc get secret -n prometheus-project` 命令。

## <a name="start-prometheus-and-alertmanager"></a>開始 Prometheus 和 Alertmanager
移至 [ [openshift]/[原始](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)] 存放庫，並下載[prometheus-yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml)範本。 輸入下列設定，將範本套用至 prometheus-project：
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus-yaml 檔案是 OpenShift 範本。 它會建立 Prometheus 實例，其前面會有一個 oauth proxy，另一個則是使用 oauth-proxy 保護的 Alertmanager 實例。 在此範本中，已將 oauth-proxy 設定為允許任何可「取得」 prometheus 專案命名空間的使用者（請參閱 `-openshift-sar` 旗標）。

> [!NOTE]
> 若要確認 [prom] StatefulSet 是否有相等的所需和目前的數目複本，請執行 `oc get statefulset -n prometheus-project` 命令。 若要檢查項目中的所有資源，請執行 `oc get all -n prometheus-project` 命令。

## <a name="add-permissions-to-allow-service-discovery"></a>新增許可權以允許服務探索

輸入下列內容來建立 prometheus-sdrole yml 檔案：
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
若要將範本套用至您要允許服務探索的所有專案，請執行下列命令：
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> 若要確認已正確建立 Role 和接著，請執行 `oc get role` 和 `oc get rolebinding` 命令。

## <a name="optional-deploy-example-application"></a>選擇性：部署範例應用程式

所有作業都在運作中，但沒有任何計量來源。 移至 Prometheus URL （ https://prom-prometheus-project.apps 。*隨機識別碼*。*region*. azmosa.io/）。 您可以使用下列命令來尋找它：

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 請記得將 HTTPs://首碼新增至主機名稱的開頭。

[**狀態 > 服務探索**] 頁面將會顯示0/0 作用中的目標。

若要部署範例應用程式，以在/metrics 端點下公開基本的 Python 計量，請執行下列命令：
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
在部署之後的30秒內，新的應用程式應該會在 [服務探索] 頁面上顯示為有效的目標。

如需詳細資訊，請選取 [**狀態**  >  **目標**]。

> [!NOTE]
> 針對每個成功剪輯的目標，Prometheus 會新增 [向上] 計量中的資料點。 選取左上角的 [ **Prometheus** ] **，輸入做**為運算式，然後選取 [**執行**]。

## <a name="next-steps"></a>後續步驟

您可以將自訂 Prometheus 檢測新增至您的應用程式。 可簡化 Prometheus 計量準備的 Prometheus 用戶端程式庫已準備好用於不同的程式設計語言。

如需詳細資訊，請參閱下列 GitHub 程式庫：

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
