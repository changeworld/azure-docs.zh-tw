---
title: '使用 Azure Data CLI 建立資料控制器 (azdata) '
description: 在您已經使用 Azure Data CLI (azdata) 建立的一般多節點 Kubernetes 叢集中建立 Azure Arc 資料控制器。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a405a7a32b1f54732589fb14e34ff52db4992028
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761680"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>使用 Azure Data CLI 建立 Azure Arc 資料控制器 (azdata) 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>必要條件

請參閱 [建立 Azure Arc 資料控制器](create-data-controller.md) 的主題，以取得總覽資訊。

若要使用 Azure 資料 CLI 建立 Azure Arc 資料控制器，您必須安裝 Azure 資料 CLI。

   [安裝 Azure Data CLI](install-client-tools.md)

無論您選擇哪一個目標平臺，您都必須先設定下列環境變數，然後再建立資料控制器系統管理員使用者。 您可以視需要將這些認證提供給需要系統管理員存取資料控制器的其他人員。

**AZDATA_USERNAME** -您選擇的使用者名稱做為資料控制器系統管理員使用者。 範例： `arcadmin`

**AZDATA_PASSWORD** -您為數據控制器系統管理員使用者選擇的密碼。 密碼長度必須至少為8個字元，且包含下列四個集合中的三個字元：大寫字母、小寫字母、數位和符號。

### <a name="linux-or-macos"></a>Linux 或 macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

您將需要連線至 Kubernetes 叢集並進行驗證，並在開始建立 Azure Arc 資料控制器之前，先選取現有的 Kubernetes 內容。 連接到 Kubernetes 叢集或服務的方式會有所不同。 請參閱您所使用的 Kubernetes 散發或服務檔，以瞭解如何連線到 Kubernetes API 伺服器。

您可以檢查是否有目前的 Kubernetes 連線，並使用下列命令確認您目前的內容。

```console
kubectl get namespace

kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>建立 Azure Arc 資料控制器

> [!NOTE]
> 您可以 `--namespace` 在下列範例中，針對 azdata arc dc create 命令的參數使用不同的值，但請務必針對 `--namespace parameter` 下列所有其他命令中的，使用該命名空間名稱。

根據您的目標平臺來設定您的建立，請遵循下面的適當區段。

[在 Azure Kubernetes Service (AKS) 上建立 ](#create-on-azure-kubernetes-service-aks)

[在 Azure Stack Hub 上的 AKS 引擎上建立](#create-on-aks-engine-on-azure-stack-hub)

[在 AKS 上建立 Azure Stack HCI](#create-on-aks-on-azure-stack-hci)

[在 Azure Red Hat OpenShift 上建立 (ARO) ](#create-on-azure-red-hat-openshift-aro)

[在 Red Hat OpenShift 容器平臺上建立 (OCP) ](#create-on-red-hat-openshift-container-platform-ocp)

[在開放原始碼、上游 Kubernetes (kubeadm) 上建立 ](#create-on-open-source-upstream-kubernetes-kubeadm)

[在 AWS 彈性 Kubernetes 服務上建立 (EKS) ](#create-on-aws-elastic-kubernetes-service-eks)

[在 Google Cloud Kubernetes Engine Service (GKE) 上建立 ](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 上建立

根據預設，AKS 部署設定檔會使用 `managed-premium` 儲存類別。 `managed-premium`只有當您的 vm 已使用具有高階磁片的 vm 映射進行部署時，儲存體類別才會運作。

如果您要將 `managed-premium` 作為儲存體類別使用，您可以執行下列命令來建立資料控制器。 以您的資源組名、訂用帳戶識別碼和 Azure 位置取代命令中的預留位置。

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

如果您不確定要使用哪一個儲存類別，則應該使用 `default` 支援的儲存體類別，而不論您使用哪一種 VM 類型。 它不會提供最快的效能。

如果您想要使用 `default` 儲存類別，您可以執行此命令：

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

執行命令之後，請繼續 [監視建立狀態](#monitoring-the-creation-status)。

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>在 Azure Stack Hub 上的 AKS 引擎上建立

根據預設，部署設定檔會使用 `managed-premium` 儲存類別。 `managed-premium`只有當您的工作者 vm 是使用在 Azure Stack Hub 上具有 premium 磁片的 VM 映射部署時，儲存體類別才會運作。

您可以執行下列命令，使用 managed premium 儲存類別來建立資料控制器：

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

如果您不確定要使用哪一個儲存類別，則應該使用 `default` 支援的儲存體類別，而不論您使用哪一種 VM 類型。 在 Azure Stack Hub 中，高階磁片和標準磁片都是由相同的儲存體基礎結構支援。 因此，它們預期會提供相同的一般效能，但會有不同的 IOPS 限制。

如果您想要使用 `default` 儲存類別，您可以執行此命令。

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

執行命令之後，請繼續 [監視建立狀態](#monitoring-the-creation-status)。

### <a name="create-on-aks-on-azure-stack-hci"></a>在 AKS 上建立 Azure Stack HCI

根據預設，部署設定檔會使用名為的儲存類別 `default` 和服務類型 `LoadBalancer` 。

您可以執行下列命令，使用 `default` 儲存類別和服務類型建立資料控制站 `LoadBalancer` 。

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

執行命令之後，請繼續 [監視建立狀態](#monitoring-the-creation-status)。


### <a name="create-on-azure-red-hat-openshift-aro"></a>在 Azure Red Hat OpenShift 上建立 (ARO) 

若要在 Azure Red Hat OpenShift 上建立資料控制器，您必須對叢集執行下列命令，以放寬安全性限制。 這是暫時的需求，未來將會移除。
> [!NOTE]
>   在這裡和下列命令中，使用相同的命名空間 `azdata arc dc create` 。 範例為 `arc` 。

首先，從 [GitHub](https://github.com/microsoft/azure_arc/tree/master/arc_data_services/deploy/yaml) 下載自訂安全性內容條件約束 (SCC) ，並將其套用至您的叢集。

您可以執行下列命令來建立資料控制器：
> [!NOTE]
>   在這裡與上述命令中使用相同的命名空間 `oc adm policy add-scc-to-user` 。 範例為 `arc` 。

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

執行命令之後，請繼續 [監視建立狀態](#monitoring-the-creation-status)。

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>在 Red Hat OpenShift 容器平臺上建立 (OCP) 


> [!NOTE]
> 如果您在 Azure 上使用 Red Hat OpenShift 容器平臺，建議使用最新的可用版本。

若要在 Red Hat OpenShift 容器平臺上建立資料控制器，您必須對叢集執行下列命令，以放寬安全性限制。 這是暫時的需求，未來將會移除。
> [!NOTE]
>   在這裡和下列命令中，使用相同的命名空間 `azdata arc dc create` 。 範例為 `arc` 。

```console
oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
```

您也必須執行下列命令，以判斷要使用哪一個儲存類別。

```console
kubectl get storageclass
```

首先，藉由執行下列命令，從建立以 azure openshift 部署設定檔為基礎的新自訂部署設定檔。 此命令會 `custom` 在您目前的工作目錄中建立目錄，並 `control.json` 在該目錄中建立自訂的部署設定檔。

使用 `azure-arc-openshift` OpenShift 容器平臺的設定檔。

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```
使用 `azure-arc-azure-openshift` Azure RedHat Open Shift 的設定檔。

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

現在，請 `<storageclassname>` 在下列命令中，以您想要使用的儲存類別名稱取代，以設定所需的儲存類別，以供您執行 `kubectl get storageclass` 上述命令所決定。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

根據預設，azure arc openshift 部署設定檔會使用 `NodePort` 做為服務類型。 如果您使用與負載平衡器整合的 OpenShift 叢集，您可以使用下列命令，將設定變更為使用 LoadBalancer 服務類型：

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

使用 OpenShift 時，您可能會想要以 OpenShift 中的預設安全性原則來執行，或想要在一般情況下鎖定環境。 您可以選擇性地選擇停用某些功能，以將部署時所需的許可權最小化，並在執行時間執行下列命令。

此命令會停用有關 pod 的計量集合。 如果您停用此功能，您將無法查看 Grafana 儀表板中的 pod 相關度量。 預設值為 true。

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

此命令會停用有關節點的計量集合。 如果您停用此功能，您將無法在 Grafana 儀表板中看到有關節點的度量。 預設值為 true。

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

此命令會停用記憶體傾印進行疑難排解的能力。
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

現在您可以使用下列命令來建立資料控制器。
> [!NOTE]
>   在這裡與上述命令中使用相同的命名空間 `oc adm policy add-scc-to-user` 。 範例為 `arc` 。

> [!NOTE]
>   `--path`參數應該指向檔案本身上不含 control.js的 control.js_目錄_。


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

執行命令之後，請繼續 [監視建立狀態](#monitoring-the-creation-status)。

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>在開放原始碼、上游 Kubernetes (kubeadm) 上建立

根據預設，kubeadm 部署設定檔會使用名為的儲存類別 `local-storage` 和服務類型 `NodePort` 。 如果可接受這種情況，您可以略過下列指示，以設定所需的儲存類別和服務類型，並立即執行 `azdata arc dc create` 下列命令。

如果您想要自訂您的部署設定檔，以指定特定的儲存類別和/或服務類型，請執行下列命令，根據 kubeadm 部署設定檔建立新的自訂部署設定檔。 此命令會 `custom` 在您目前的工作目錄中建立目錄，並 `control.json` 在該目錄中建立自訂的部署設定檔。

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

您可以藉由執行下列命令來查詢可用的儲存類別。

```console
kubectl get storageclass
```

現在，請 `<storageclassname>` 在下列命令中，以您想要使用的儲存類別名稱取代，以設定所需的儲存類別，以供您執行 `kubectl get storageclass` 上述命令所決定。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

根據預設，kubeadm 部署設定檔會使用 `NodePort` 做為服務類型。 如果您使用與負載平衡器整合的 Kubernetes 叢集，您可以使用下列命令來變更設定。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

現在您可以使用下列命令來建立資料控制器。

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

執行命令之後，請繼續 [監視建立狀態](#monitoring-the-creation-status)。

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>在 AWS 彈性 Kubernetes 服務上建立 (EKS) 

依預設，EKS 儲存類別為， `gp2` 而且服務類型為 `LoadBalancer` 。

執行下列命令，以使用提供的 EKS 部署設定檔來建立資料控制器。

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

執行命令之後，請繼續 [監視建立狀態](#monitoring-the-creation-status)。

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>在 Google Cloud Kubernetes Engine Service (GKE) 上建立

依預設，GKE 儲存類別為， `standard` 而且服務類型為 `LoadBalancer` 。

執行下列命令，以使用提供的 GKE 部署設定檔來建立資料控制器。

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

執行命令之後，請繼續 [監視建立狀態](#monitoring-the-creation-status)。

## <a name="monitoring-the-creation-status"></a>監視建立狀態

建立控制器需要幾分鐘的時間才能完成。 您可以使用下列命令來監視另一個終端機視窗中的進度：

> [!NOTE]
>  下列範例命令假設您已建立具有名稱的資料控制器和 Kubernetes 命名空間 `arc` 。 如果您使用不同的命名空間/資料控制器名稱，您可以 `arc` 將取代為您的名稱。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

您也可以執行如下所示的命令，檢查任何特定 pod 的建立狀態。 這特別適用于針對任何問題進行疑難排解。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>針對建立問題進行疑難排解

如果您在建立麻煩很快找上門時遇到任何問題，請參閱 [疑難排解指南](troubleshoot-guide.md)。
