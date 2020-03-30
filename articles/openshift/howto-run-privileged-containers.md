---
title: 在 Azure 紅帽 OpenShift 群集中運行特權容器 |微軟文檔
description: 運行特權容器以監視安全性和合規性。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro， 開移， 水賽， 扭鎖， 紅帽子
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271368"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 叢集中執行具特殊權限的容器

不能在 Azure 紅帽 OpenShift 群集上運行任意特權容器。
允許在 ARO 群集上運行兩個安全監視和合規性解決方案。
本文檔介紹與安全產品供應商的通用 OpenShift 部署文檔的差異。


在按照供應商的說明操作之前，請通讀這些說明。
以下特定于產品步驟中的節標題直接引用供應商文檔中的節標題。

## <a name="before-you-begin"></a>開始之前

大多數安全產品的文檔假定您具有群集管理許可權。
客戶管理員在 Azure 紅帽 OpenShift 中沒有擁有權限。 修改群集範圍資源所需的許可權是有限的。

首先，通過運行`oc get scc`，確保使用者以客戶管理員身份登錄到群集。 作為客戶管理組成員的所有使用者都有權查看群集上的安全上下文約束 （SCC）。

接下來，確保`oc`二進位版本為`3.11.154`。
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>針對水安全的產品特定步驟
將要修改的基本說明可以在[Aqua 安全部署文檔中](https://docs.aquasec.com/docs/openshift-red-hat)找到。 此處的步驟將與 Aqua 部署文檔一起運行。

第一步是對要更新所需的 SCC 進行批號。 這些注釋阻止群集的同步 Pod 還原對這些 SSC 的任何更改。

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>第 1 步：準備先決條件
請記住以 ARO 客戶管理員而不是群集管理員角色登錄到群集。

創建專案和服務帳戶。
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

使用以下命令將客戶管理員群集角色指派給 Aqua 帳戶，而不是分配群集讀取器角色。
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

繼續按照步驟 1 中的剩餘說明操作。  這些說明描述了為 Aqua 註冊表設置機密。

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>第 2 步：部署水族伺服器、資料庫和閘道
按照 Aqua 文檔中提供的步驟安裝水主控台.

修改提供的`aqua-console.yaml`。  刪除標記為 的前兩個物件，`kind: ClusterRole`和`kind: ClusterRoleBinding`。  不會創建這些資源，因為客戶管理員此時沒有修改`ClusterRole`和`ClusterRoleBinding`物件的許可權。

第二個修改將到`kind: Route`的`aqua-console.yaml`部分。 替換`kind: Route``aqua-console.yaml`檔中物件的以下 yaml。
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

按照其餘說明操作。

### <a name="step-3-login-to-the-aqua-server"></a>第 3 步：登錄到水族伺服器
此部分不會以任何方式修改。  請遵循 Aqua 文檔。

使用以下命令獲取 Aqua 主控台位址。
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>第 4 步：部署水上強制器
部署執行者時設置以下欄位：

| 欄位          | 值         |
| -------------- | ------------- |
| 協調器   | OpenShift     |
| ServiceAccount | 水帳戶  |
| 隨附此逐步解說的專案        | 水上安全 |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>棱鏡雲/Twistlock 的產品特定步驟

我們將修改的基本說明可在[Prisma 雲部署文檔中](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)找到

首先安裝"安裝`twistcli`棱鏡雲"和"下載棱鏡雲軟體"部分中所述的工具。

創建新的 OpenShift 專案
```
oc new-project twistlock
```

跳過可選部分"將棱鏡雲映射推送到專用註冊表"。 它不會在 Azure 紅帽開放移位上工作。 改用連線註冊表。

您可以按照官方文檔操作，同時應用下面描述的更正。
從"安裝主控台"部分開始。

### <a name="install-console"></a>安裝主控台

在`oc create -f twistlock_console.yaml`步驟 2 中，創建命名空間時將收到錯誤。
您可以安全地忽略它，命名空間以前已使用 命令`oc new-project`創建。

用於`azure-disk`存儲類型。

### <a name="create-an-external-route-to-console"></a>創建到主控台的外部路由

如果您更喜歡 oc 命令，您可以按照文檔或下面的說明進行操作。
將以下路由定義複製到電腦上的名為 twistlock_route.yaml 的檔
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
然後運行：
```
oc create -f twistlock_route.yaml
```

您可以使用以下命令獲取分配給 Twistlock 主控台的 URL：`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>配置主控台

請遵循 Twistlock 文檔。

### <a name="install-defender"></a>安裝防禦器

在`oc create -f defender.yaml`步驟 2 中，在創建群集角色和群集角色綁定時，您將收到錯誤。
因此您可以忽略。

防禦者將只部署在計算節點上。 您不必使用節點選擇器來限制它們。
