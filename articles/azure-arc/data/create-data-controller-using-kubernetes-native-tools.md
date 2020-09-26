---
title: 使用 Kubernetes 工具建立資料控制器
description: 使用 Kubernetes 工具建立資料控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c5a2aa6ca75e352a824716c19af923c8628efde
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342013"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>使用 Kubernetes 工具建立 Azure Arc 資料控制器

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisites

請參閱 [建立 Azure Arc 資料控制器](create-data-controller.md) 的主題，以取得總覽資訊。

若要使用 Kubernetes 工具建立 Azure Arc 資料控制器，您必須安裝 Kubernetes 工具。  本文中的範例將使用 `kubectl` ，但類似的方法可與其他 Kubernetes 工具搭配使用，例如 Kubernetes 儀表板、 `oc` ，或是 `helm` 您熟悉這些工具和 Kubernetes yaml/json。

[安裝 kubectl 工具](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> 建立如下所示 Azure Arc 資料控制器的部分步驟需要 Kubernetes 叢集系統管理員許可權。  如果您不是 Kubernetes 叢集系統管理員，就必須讓 Kubernetes 叢集系統管理員代表您執行這些步驟。

## <a name="overview"></a>概觀

建立 Azure Arc 資料控制器具有下列高階步驟：
1. 為 Arc 資料控制器、Azure SQL 受控實例和于 postgresql 超大規模建立自訂資源定義。 **[需要 Kubernetes 叢集系統管理員許可權]**
2. 建立將在其中建立資料控制器的命名空間。 **[需要 Kubernetes 叢集系統管理員許可權]**
3. 建立啟動載入器服務，包括複本集、服務帳戶、角色和角色系結。
4. 建立資料控制器系統管理員使用者名稱和密碼的秘密。
5. 建立資料控制器。
   
## <a name="create-the-custom-resource-definitions"></a>建立自訂資源定義

執行下列命令以建立自訂資源定義。  **[需要 Kubernetes 叢集系統管理員許可權]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>建立將在其中建立資料控制器的命名空間

執行類似下列的命令，以建立將在其中建立資料控制器的新專用命名空間。  在此範例和本文的其餘範例中，將會使用的命名空間名稱 `arc` 。 如果您選擇使用不同的名稱，請在整個中使用相同的名稱。

```console
kubectl create namespace arc
```

如果其他人使用的命名空間不是叢集系統管理員，我們建議您建立命名空間管理員角色，並透過角色系結將該角色授與這些使用者。  命名空間管理員應該具有命名空間的完整許可權。  稍後會提供更多詳細資訊給使用者，以提供更細微的角色型存取。

## <a name="create-the-bootstrapper-service"></a>建立啟動載入器服務

啟動載入器服務會處理連入要求，以建立、編輯和刪除自訂資源，例如資料控制器、SQL 受控實例或于 postgresql 超大規模伺服器群組。

執行下列命令來建立啟動載入器服務、啟動載入器服務的服務帳戶，以及啟動載入器服務帳戶的角色和角色系結。

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/bootstrapper.yaml
```

使用下列命令確認啟動載入器 pod 正在執行。  您可能需要執行數次，直到狀態變更為為止 `Running` 。

```console
kubectl get pod --namespace arc
```

Yaml 範本檔案預設為從 Microsoft Container Registry 提取啟動載入器容器映射 (MCR) 。  如果您的環境沒有直接存取 Microsoft Container Registry 的許可權，您可以執行下列動作：
- 遵循下列步驟， [從 Microsoft Container registry 提取容器映射，並將其推送至私人容器](offline-deployment.md)登錄。
- 為您的私人容器登錄[建立映射提取秘密](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin)。
- 將影像提取秘密新增至啟動載入器容器。 請參閱以下的範例。
- 變更啟動載入器映射的映射位置。 請參閱以下的範例。

下列範例假設您已建立映射提取秘密名稱， `regcred` 如 Kubernetes 檔中所示。

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-sep-2020 <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>建立資料控制站系統管理員的秘密

資料控制器系統管理員使用者名稱和密碼是用來驗證資料控制器 API，以執行系統管理功能。  選擇安全的密碼，並只與需要擁有叢集系統管理員許可權的密碼共用。

Kubernetes 秘密會儲存為 base64 編碼的字串，一個用於使用者名稱，另一個用於密碼。

您可以使用線上工具，以 base64 編碼您想要的使用者名稱和密碼，或根據您的平臺使用內建的 CLI 工具。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

當您編碼使用者名稱和密碼之後，您可以建立以 [範本](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/controller-login-secret.yaml) 檔案為基礎的檔案，並將使用者名稱和密碼值取代為您自己的值。

然後執行下列命令來建立秘密。

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>建立資料控制器

現在您已準備好建立資料控制器本身。

首先，在電腦本機上建立 [範本](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/data-controller.yaml) 檔案的複本，讓您可以修改部分設定。

視需要編輯下列內容：

**必填**
- **位置**：將此變更為將儲存資料控制器 _中繼資料_ 的 Azure 位置。  您可以在 [建立資料控制器總覽](create-data-controller.md) 文章中查看可用的 Azure 位置清單。
- **resourceGroup**：您要在 Azure Resource Manager 中建立資料控制器 azure 資源的 azure 資源群組。  此資源群組通常應該已經存在，但在您將資料上傳至 Azure 之前，並不需要此資源群組。
- **訂**用帳戶：您要在其中建立 azure 資源之訂用帳戶的 azure 訂用帳戶 GUID。

**建議您複習並可能變更預設值**
- **儲存體 .。。className**：用於資料控制器資料和記錄檔的儲存類別。  如果您不確定 Kubernetes 叢集中的可用儲存類別，您可以執行下列命令： `kubectl get storageclass` 。  預設值是， `default` 假設有一個儲存類別存在，而且其名稱 `default` 不是預設的儲存類別。 _is_  注意：有兩個要設定為所需儲存類別的 className 設定-一個用於資料，另一個用於記錄。
- **serviceType**： `NodePort` 如果您不使用 LoadBalancer，請將服務類型變更為。  注意：有兩個 serviceType 設定需要變更。

**選**
- **名稱**：資料控制器的預設名稱 `arc` ，但您可以視需要加以變更。
- **displayName**：將此值設定為與檔案頂端的名稱屬性相同的值。
- 登錄 **： Microsoft**Container registry 是預設值。  如果您要從 Microsoft Container Registry 提取映射，並將 [其推送至私人容器](offline-deployment.md)登錄，請在此輸入您的登錄 IP 位址或 DNS 名稱。
- **dockerRegistry**：必要時，用來從私人容器登錄中提取映射的映射提取秘密。
- 存放**庫**： Microsoft Container Registry 上的預設存放庫 `arcdata` 。  如果您使用私人容器登錄，請輸入資料夾/存放庫的路徑，其中包含已啟用 Azure Arr 的資料服務容器映射。
- **imageTag**：目前最新版本標記是範本中預設的標籤，但如果您想要使用較舊的版本，您可以變更它。

完成的資料控制器 yaml 檔範例：
```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: mssql-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-sep-2020
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

將編輯過的檔案儲存在本機電腦上，然後執行下列命令來建立資料控制器：

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>監視建立狀態

建立控制器需要幾分鐘的時間才能完成。 您可以使用下列命令來監視另一個終端機視窗中的進度：

> [!NOTE]
>  下列範例命令假設您已建立具有名稱的資料控制器和 Kubernetes 命名空間 `arc` 。  如果您使用不同的命名空間/資料控制器名稱，您可以 `arc` 將取代為您的名稱。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

您也可以執行如下所示的命令，檢查任何特定 pod 的建立狀態。  這特別適用于針對任何問題進行疑難排解。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

適用于 Azure Data Studio 的 Azure Arc 延伸模組可讓您逐步瞭解如何設定啟用 Azure Arc 的 Kubernetes，並將其設定為監視包含範例 SQL 受控執行個體 yaml 檔案的 git 存放庫。 當所有專案都連線時，會將新的 SQL 受控執行個體部署到您的 Kubernetes 叢集。

請參閱 Azure Data Studio Azure Arc 延伸模組中的「 **使用已啟用 Azure Arc 的 Kubernetes 和 Flux 筆記本部署 SQL 受控執行個體** 。

## <a name="troubleshooting-creation-problems"></a>針對建立問題進行疑難排解

如果您在建立麻煩很快找上門時遇到任何問題，請參閱 [疑難排解指南](troubleshoot-guide.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Kubernetes 原生工具建立 SQL 受控實例](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [使用 Kubernetes 原生工具建立于 postgresql 超大規模伺服器群組](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
