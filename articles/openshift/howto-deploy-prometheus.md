---
title: 在 Azure 紅帽 OpenShift 群集中部署獨立的 Prometheus 實例 |微軟文檔
description: 在 Azure 紅帽 OpenShift 群集中創建 Prometheus 實例，以監視應用程式的指標。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: 普羅米圖斯， aro， 開班， 指標， 紅帽子
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "69875128"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>在 Azure 紅帽開放Shift 群集中部署獨立的 Prometheus 實例

本文介紹如何配置在 Azure 紅帽 OpenShift 群集中使用服務發現的獨立 Prometheus 實例。

> [!NOTE]
> 客戶管理員不需要訪問 Azure 紅帽 OpenShift 群集。

目標設置：

- 一個專案（普羅米蘇斯專案），其中包含普羅米蘇斯和警報管理器。
- 兩個專案（應用專案1和應用專案2），其中包含要監視的應用程式。

您將在本地準備一些普羅米蘇斯設定檔。 創建新資料夾以存儲它們。 設定檔作為機密存儲在群集中，以防以後將機密權杖添加到群集中。

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>使用 OC 工具登錄到群集

1. 打開 Web 瀏覽器，然後轉到群集的 Web 主控台https://openshift（。*隨機 ID*。*區域*.azmosa.io）。
2. 利用您的 Azure 認證登入。
3. 在右上角選擇您的使用者名，然後選擇 **"複製登錄命令**"。
4. 將使用者名粘貼到要使用的終端中。

> [!NOTE]
> 要查看是否登錄到正確的群集，請運行該`oc whoami -c`命令。

## <a name="prepare-the-projects"></a>準備專案

要創建專案，請運行以下命令：
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 可以使用`-n`或`--namespace`參數，或通過運行`oc project`命令選擇活動專案。

## <a name="prepare-the-prometheus-configuration-file"></a>準備普羅米圖斯設定檔
通過輸入以下內容創建 prometheus.yml 檔：
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
通過輸入以下配置創建名為 Prom 的機密：
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

普羅米底斯.yml檔是一個基本的普羅米底斯設定檔。 它設置間隔並在三個專案中配置自動探索（prometheus 專案、應用專案 1、應用專案 2）。 在以前的設定檔中，自動探索的終結點在沒有身份驗證的情況下通過 HTTP 進行刮擦。

有關刮削端點的詳細資訊，請參閱[普羅米塞烏斯風景圖](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)。


## <a name="prepare-the-alertmanager-config-file"></a>準備警報管理器設定檔
通過輸入以下內容創建警報管理器.yml 檔：
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
通過輸入以下配置創建名為 Prom-Alert 的機密：
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

警報管理器.yml 是警報管理器設定檔。

> [!NOTE]
> 要驗證前面的兩個步驟，請運行該`oc get secret -n prometheus-project`命令。

## <a name="start-prometheus-and-alertmanager"></a>啟動普羅米古斯和警報管理器
轉到[開班/原發存儲庫](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)並下載[prometheus 獨立.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml)範本。 通過輸入以下配置，將範本應用於 prometheus 專案：
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
prometheus 獨立.yaml 檔是一個 OpenShift 範本。 它將創建一個 Prometheus 實例，其前面有 oauth 代理，並且使用 oauth 代理保護了警報管理器實例。 在此範本中，auth 代理配置為允許任何可以"獲取"普羅米圖斯專案命名空間的使用者（請參閱`-openshift-sar`標誌）。

> [!NOTE]
> 要驗證 promStatefulSet 是否具有相等的"渴望"和"當前編號`oc get statefulset -n prometheus-project`副本"，請運行該命令。 要檢查項目中的所有資源，請運行該`oc get all -n prometheus-project`命令。

## <a name="add-permissions-to-allow-service-discovery"></a>添加允許服務發現的許可權

通過輸入以下內容創建 prometheus-sdrole.yml 檔：
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
要將範本應用於要從中允許服務發現的所有專案，請運行以下命令：
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> 要驗證角色和角色綁定是否正確創建，請運行`oc get role`和`oc get rolebinding`命令。

## <a name="optional-deploy-example-application"></a>可選：部署應用程式範例

一切工作，但沒有指標源。 轉到普羅米蘇斯 URL （.https://prom-prometheus-project.apps *隨機 ID*。*區域*.azmosa.io/）。 您可以使用以下命令找到它：

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 請記住將HTTPs://首碼添加到主機名稱的開頭。

**狀態>服務發現**"頁將顯示 0/0 活動目標。

要部署在 /metrics 終結點下公開基本 Python 指標的應用程式範例，請運行以下命令：
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
新應用程式應在部署後 30 秒內在"服務發現"頁上顯示為有效目標。

有關詳細資訊，請選擇 **"狀態** > **目標**"。

> [!NOTE]
> 對於每個成功刮擦的目標，Prometheus 都會在上升指標中添加一個資料點。 選擇左上角的**Prometheus，****向上**輸入作為運算式，然後選擇 **"執行**"。

## <a name="next-steps"></a>後續步驟

您可以將自訂普羅米底斯檢測添加到應用程式。 Prometheus 用戶端庫簡化了 Prometheus 指標的編制，可用於不同的程式設計語言。

有關詳細資訊，請參閱以下 GitHub 庫：

 - [JAVA](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [移至](https://github.com/prometheus/client_golang)
 - [紅寶石](https://github.com/prometheus/client_ruby)
