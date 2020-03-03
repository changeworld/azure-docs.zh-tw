---
title: 在 Azure Red Hat OpenShift 叢集中執行具有特殊許可權的容器 |Microsoft Docs
description: 執行具有特殊許可權的容器，以監視安全性和合規性。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro、openshift、aquasec、twistlock、red hat
ms.openlocfilehash: 5d28a19126c9b7ae4ef7afe2a6b69bd4a13e0c83
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228247"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 叢集中執行具特殊權限的容器

您無法在 Azure Red Hat OpenShift 叢集上執行任何特殊許可權容器。
允許在 ARO 叢集上執行兩個安全性監視和合規性解決方案。
本檔說明安全性產品廠商的一般 OpenShift 部署檔差異。


請先閱讀這些指示，再遵循廠商的指示。
下列產品特定步驟中的章節標題會直接參考廠商檔中的章節標題。

## <a name="before-you-begin"></a>開始之前

大部分安全性產品的檔假設您擁有叢集系統管理員許可權。
客戶管理員在 Azure Red Hat OpenShift 中沒有擁有權限。 修改全叢集資源所需的許可權有限。

首先，藉由執行 `oc get scc`，確保使用者以客戶系統管理員的身分登入叢集。 屬於 customer admin 群組成員的所有使用者都有權在叢集上查看安全性內容條件約束（SCCs）。

接下來，請確定 `3.11.154``oc` 二進位版本。
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>淺綠色安全性的產品特定步驟
您可以在青色[安全性部署檔](https://docs.aquasec.com/docs/openshift-red-hat)中找到即將修改的基本指示。 這裡的步驟將與綠色部署檔搭配執行。

第一個步驟是標注將更新的必要 SCCs。 這些批註會防止叢集的同步處理 Pod 還原這些 SSCs 的任何變更。

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>步驟1：準備必要條件
請記得以 ARO 客戶系統管理員的身分登入叢集，而不是叢集系統管理員角色。

建立專案和服務帳戶。
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

請使用下列命令，將客戶-系統管理員-叢集角色指派給青色帳戶，而不是指派叢集讀取器角色。
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

繼續遵循步驟1中的其餘指示。  這些指示說明如何設定淺綠色登錄的密碼。

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>步驟2：部署淺綠色伺服器、資料庫和閘道
請遵循綠色檔中提供的步驟來安裝淺 yaml。

修改提供的 `aqua-console.yaml`。  移除標示為、`kind: ClusterRole` 和 `kind: ClusterRoleBinding`的前兩個物件。  這些資源將不會建立，因為客戶管理員目前沒有許可權可修改 `ClusterRole` 和 `ClusterRoleBinding` 物件。

第二個修改將會指向 `aqua-console.yaml`的 `kind: Route` 部分。 取代 `aqua-console.yaml` 檔案中 `kind: Route` 物件的下列 yaml。
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

依照其餘指示進行。

### <a name="step-3-login-to-the-aqua-server"></a>步驟3：登入淺綠色伺服器
這一節不會以任何方式修改。  請遵循淺綠色檔。

使用下列命令來取得淺綠色的主控台位址。
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>步驟4：部署淺青色 Enforcers
部署 enforcers 時，請設定下欄欄位：

| 欄位          | 值         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | 青色-帳戶  |
| 專案        | 淺綠色-安全性 |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Prisma Cloud/Twistlock 的產品特定步驟

您可以在[Prisma 雲端部署檔](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)中找到我們要修改的基本指示。

請先依照 < Install Prisma Cloud 》和 < 下載 Prisma 雲端軟體一節中所述的方式，安裝 `twistcli` 工具。

建立新的 OpenShift 專案
```
oc new-project twistlock
```

略過選擇性區段「將 Prisma 的雲端映射推送至私人登錄」。 它無法在 Azure Red Hat Openshift 上使用。 請改用線上登錄。

您可以遵循官方檔，同時套用下面所述的更正。
從 [安裝主控台] 區段開始。

### <a name="install-console"></a>安裝主控台

在步驟 2 `oc create -f twistlock_console.yaml` 期間，您會在建立命名空間時收到錯誤。
您可以放心地忽略它，先前已使用 `oc new-project` 命令建立命名空間。

### <a name="create-an-external-route-to-console"></a>建立主控台的外部路由

如果您偏好使用 oc 命令，可以遵循檔或下列指示。
將下列路由定義複製到您電腦上 twistlock_route 名為 yaml 的檔案中。
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
然後執行：
```
oc create -f twistlock_route.yaml
```

您可以使用下列命令取得指派給 Twistlock 主控台的 URL： `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>設定主控台

遵循 Twistlock 檔。

### <a name="install-defender"></a>安裝 Defender

在步驟 2 `oc create -f defender.yaml` 期間，您會在建立叢集角色和叢集角色系結時收到錯誤。
您可以忽略它們。

防禦者只會部署在計算節點上。 您不需要使用節點選取器來限制它們。
