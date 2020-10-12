---
title: 在 Azure Red Hat OpenShift 叢集中執行具特殊許可權的容器 |Microsoft Docs
description: 執行具有特殊許可權的容器來監視安全性和合規性。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro、openshift、aquasec、twistlock、red hat
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78271368"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 叢集中執行具特殊權限的容器

您無法在 Azure Red Hat OpenShift 叢集上執行任意特殊許可權的容器。
有兩個安全性監視和合規性解決方案可在 ARO 叢集上執行。
本檔說明安全性產品廠商的一般 OpenShift 部署檔的差異。


遵循廠商的指示之前，請先仔細閱讀這些指示。
下列產品特定步驟中的章節標題直接參考廠商檔中的章節標題。

## <a name="before-you-begin"></a>開始之前

大部分安全性產品的檔會假設您有叢集系統管理員許可權。
客戶管理員沒有 Azure Red Hat OpenShift 中的擁有權限。 修改全叢集資源所需的許可權有限。

首先，執行，以確保使用者以客戶系統管理員身分登入叢集 `oc get scc` 。 屬於客戶系統管理員群組成員的所有使用者都有許可權可查看叢集上 (SCCs) 的安全性內容條件約束。

接下來，請確定 `oc` 二進位版本是 `3.11.154` 。
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>綠色安全性的產品特定步驟
即將修改的基本指示可在「青色 [安全性部署」檔](https://docs.aquasec.com/docs/openshift-red-hat)中找到。 這裡的步驟將會與青色部署檔搭配執行。

第一個步驟是批註即將更新的必要 SCCs。 這些批註會防止叢集的同步處理 Pod 還原這些 SSCs 的任何變更。

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>步驟1：準備必要條件
請記得以 ARO 客戶系統管理員身分登入叢集，而不是以叢集管理員角色登入。

建立專案和服務帳戶。
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

您可以使用下列命令，將客戶管理叢集角色指派給青色帳戶，而不是指派叢集讀取者角色。
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

繼續遵循步驟1中的其餘指示進行。  這些指示說明如何設定綠色登錄的秘密。

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>步驟2：部署淺綠伺服器、資料庫和閘道
遵循青色檔中提供的步驟來安裝青色主控台. yaml。

修改提供的 `aqua-console.yaml` 。  移除標示為、和的前兩個物件 `kind: ClusterRole` `kind: ClusterRoleBinding` 。  這些資源不會建立，因為客戶系統管理員目前沒有許可權可修改 `ClusterRole` 和 `ClusterRoleBinding` 物件。

第二項修改將會指向的 `kind: Route` 部分 `aqua-console.yaml` 。 取代檔案中物件的下列 yaml `kind: Route` `aqua-console.yaml` 。
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

遵循其餘的指示。

### <a name="step-3-login-to-the-aqua-server"></a>步驟3：登入綠色伺服器
本節不會以任何方式修改。  遵循青色檔。

使用下列命令來取得綠色的主控台位址。
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>步驟4：部署淺綠 Enforcers
部署 enforcers 時，請設定下欄欄位：

| 欄位          | 值         |
| -------------- | ------------- |
| 協調器   | OpenShift     |
| ServiceAccount | 綠色-帳戶  |
| 專案        | 綠色-安全性 |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Prisma Cloud/Twistlock 的產品特定步驟

您可以在[Prisma 雲端部署檔](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)中找到我們要修改的基本指示

請依照「 `twistcli` 安裝 Prisma 雲端」和「下載 Prisma cloud software」一節中所述的方式來安裝此工具。

建立新的 OpenShift 專案
```
oc new-project twistlock
```

略過選擇性區段「將 Prisma 雲端映射推送至私人登錄」。 它無法在 Azure Red Hat Openshift 上運作。 請改用 online 登錄。

您可以遵循正式的檔，並套用以下所述的更正。
從 [安裝主控台] 區段開始。

### <a name="install-console"></a>安裝主控台

`oc create -f twistlock_console.yaml`在步驟2中，您會在建立命名空間時收到錯誤。
您可以放心地忽略它，先前已使用命令建立命名空間 `oc new-project` 。

用於 `azure-disk` 儲存體類型。

### <a name="create-an-external-route-to-console"></a>建立主控台的外部路由

如果您偏好使用 oc 命令，您可以遵循檔或下列指示。
將下列路由定義複製到您電腦上名為 twistlock_route yaml 的檔案。
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

您可以使用下列命令來取得指派給 Twistlock 主控台的 URL： `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>設定主控台

遵循 Twistlock 檔。

### <a name="install-defender"></a>安裝 Defender

`oc create -f defender.yaml`在步驟2中，您會在建立叢集角色和叢集角色系結時收到錯誤。
因此您可以忽略。

防禦者只會部署在計算節點上。 您不需要使用節點選取器來限制它們。
