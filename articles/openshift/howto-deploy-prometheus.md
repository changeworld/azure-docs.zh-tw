---
title: 在 Azure Red Hat OpenShift 叢集中部署 Prometheus 實例
description: 在 Azure Red Hat OpenShift 叢集中建立 Prometheus 實例，以監視應用程式的計量。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus、aro、openshift、計量、red hat
ms.openlocfilehash: 42ed8c90b35eba57fdc3db1f0ed93d44cf9a5e41
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218607"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 叢集中部署獨立的 Prometheus 實例

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 將于2022年6月30日淘汰。 支援建立新的 Azure Red Hat OpenShift 3.11 叢集會繼續到2020年11月30日。 淘汰之後，剩餘的 Azure Red Hat OpenShift 3.11 叢集將會關閉以防止安全性弱點。
> 
> 遵循本指南來 [建立 Azure Red Hat OpenShift 4](tutorial-create-cluster.md)叢集。
> 如果您有特定問題， [請洽詢我們](mailto:arofeedback@microsoft.com)。

本文說明如何設定在 Azure Red Hat OpenShift 叢集中使用服務探索的獨立 Prometheus 實例。

> [!NOTE]
> 不需要客戶對 Azure Red Hat OpenShift 叢集的存取權。

目標設定：

- 一個專案 (prometheus 專案) ，其中包含 Prometheus 和 Alertmanager。
- 有兩個專案 (應用程式 project1 和應用程式 project2) ，其中包含要監視的應用程式。

您將在本機準備一些 Prometheus 設定檔。 建立新的資料夾來儲存它們。 設定檔會以秘密的形式儲存在叢集中，以防稍後將秘密權杖新增至叢集。

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>使用 OC 工具登入叢集

1. 開啟網頁瀏覽器，然後移至叢集 (的 web 主控台 https://openshift 。*隨機識別碼*。* *azmosa.io) 。
2. 利用您的 Azure 認證登入。
3. 在右上角選取您的使用者名稱，然後選取 [ **複製登**入] 命令。
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
> 您可以使用 `-n` 或 `--namespace` 參數，或藉由執行命令來選取使用中的專案 `oc project` 。

## <a name="prepare-the-prometheus-configuration-file"></a>準備 Prometheus 設定檔
輸入下列內容以建立 prometheus yml 檔案：
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
輸入下列設定來建立名為 Prom 的秘密：
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus. yml 檔案是基本的 Prometheus 設定檔。 它會在三個專案中設定間隔並設定自動探索 (prometheus、project1、應用程式 project2) 。 在先前的設定檔中，自動探索的端點是透過 HTTP 剪輯，不需要驗證。

如需抓取端點的詳細資訊，請參閱 [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)。


## <a name="prepare-the-alertmanager-config-file"></a>準備 Alertmanager 設定檔
輸入下列內容以建立 alertmanager yml 檔案：
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
輸入下列設定來建立名為 Prom-Alerts 的秘密：
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. yml 是警示管理員設定檔案。

> [!NOTE]
> 若要確認這兩個先前的步驟，請執行 `oc get secret -n prometheus-project` 命令。

## <a name="start-prometheus-and-alertmanager"></a>開始 Prometheus 和 Alertmanager
移至 [openshift/原始存放庫](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) ，並下載 [prometheus-獨立式 yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) 範本。 輸入下列設定，以將範本套用至 prometheus 專案：
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus-獨立式 yaml 檔案是 OpenShift 範本。 它會建立 Prometheus 實例，並在其前方使用 oauth proxy 和 Alertmanager 實例，也會使用 oauth proxy 來保護。 在此範本中，oauth proxy 設定為允許任何可以 "get" prometheus 專案命名空間 (查看旗標) 的使用者 `-openshift-sar` 。

> [!NOTE]
> 若要確認 prom StatefulSet 是否有相等的所需複本和目前的數目，請執行 `oc get statefulset -n prometheus-project` 命令。 若要檢查項目中的所有資源，請執行 `oc get all -n prometheus-project` 命令。

## <a name="add-permissions-to-allow-service-discovery"></a>新增許可權以允許服務探索

輸入下列內容以建立 prometheus-sdrole yml 檔案：
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
若要將範本套用至您想要允許服務探索的所有專案，請執行下列命令：
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> 若要確認已正確建立角色和 RoleBinding，請執行 `oc get role` 和 `oc get rolebinding` 命令。

## <a name="optional-deploy-example-application"></a>選用：部署範例應用程式

一切都能正常運作，但沒有任何計量來源。 移至 Prometheus URL (https://prom-prometheus-project.apps 。*隨機識別碼*。* *azmosa.io/) 。 您可以使用下列命令來找到它：

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 請記得將 HTTPs://前置詞新增至主機名稱的開頭。

[ **狀態 > 服務探索** ] 頁面會顯示0/0 使用中目標。

若要部署範例應用程式，以在/metrics 端點下公開基本 Python 計量，請執行下列命令：
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
在部署後30秒內，新的應用程式應該會在服務探索頁面上顯示為有效的目標。

如需詳細資料，請選取 [**狀態**  >  **目標**]。

> [!NOTE]
> 針對每個成功的剪輯目標，Prometheus 會在向上計量中新增一個資料點。 選取左上角的 [ **Prometheus** ] **，輸入做** 為運算式，然後選取 [ **執行**]。

## <a name="next-steps"></a>後續步驟

您可以在應用程式中新增自訂 Prometheus 檢測。 可簡化 Prometheus 計量準備的 Prometheus 用戶端程式庫，已準備好用於不同的程式設計語言。

如需詳細資訊，請參閱下列 GitHub 程式庫：

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
