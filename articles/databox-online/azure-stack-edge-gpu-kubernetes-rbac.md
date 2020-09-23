---
title: 瞭解 Azure Stack Edge Pro 裝置上的角色型存取控制 Kubernetes |Microsoft Docs
description: 描述 Kubernetes 角色型存取控制如何在 Azure Stack Edge Pro 裝置上進行。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 1f194424a4030a2b829af6c8f5b97a3c200bd2e6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899282"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 裝置上 Kubernetes 以角色為基礎的存取控制


在您的 Azure Stack Edge Pro 裝置上，當您設定計算角色時，會建立 Kubernetes 叢集。 您可以使用 Kubernetes 角色型存取控制 (RBAC) 來限制對您裝置上叢集資源的存取。

本文概述 Kubernetes 提供的 RBAC 系統，以及如何在您的 Azure Stack Edge Pro 裝置上執行 Kubernetes RBAC。 

## <a name="rbac-for-kubernetes"></a>適用于 Kubernetes 的 RBAC

Kubernetes RBAC 可讓您指派使用者或使用者群組，以執行建立或修改資源等作業的許可權，或從執行中的應用程式工作負載來查看記錄。 這些許可權可以設定為單一命名空間，或授與整個叢集的範圍。 

當您設定 Kubernetes 叢集時，系統會建立對應到此叢集的單一使用者，而且稱為「叢集系統管理使用者」。  `kubeconfig`與叢集系統管理員使用者相關聯的檔案。 檔案 `kubeconfig` 是一個文字檔，其中包含連接到叢集以驗證使用者所需的所有設定資訊。

## <a name="namespaces-types"></a>命名空間類型

Kubernetes 資源（例如 pod 和部署）會以邏輯方式分組到命名空間中。 這些群組可讓您以邏輯方式分割 Kubernetes 叢集，並限制存取以建立、查看或管理資源。 使用者只能與其指派的命名空間內包含的資源互動。

命名空間適用于有許多使用者散佈在多個小組或專案的環境中。 如需詳細資訊，請參閱 [Kubernetes 命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)。

您的 Azure Stack Edge Pro 裝置具有下列命名空間：

- **系統命名空間** -此命名空間是核心資源所在的位置，例如 DNS 和 proxy 等網路功能，或 Kubernetes 儀表板。 您通常不會將自己的應用程式部署到此命名空間中。 使用此命名空間來偵測任何 Kubernetes 叢集問題。 

    您的裝置上有多個系統命名空間，並保留對應于這些系統命名空間的名稱。 以下是保留的系統命名空間清單： 
    - kube-系統
    - metallb-系統
    - dbe-命名空間
    - default
    - kubernetes-儀表板
    - kube-節點-租用
    - kube-public


    請務必不要針對您所建立的使用者命名空間使用任何保留名稱。 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **使用者命名空間** -這些是您可以透過 **kubectl** 建立的命名空間，或透過裝置的 PowerShell 介面，在本機部署應用程式的命名空間。
 
- **IoT Edge 命名空間** -您會連接到此 `iotedge` 命名空間，以管理透過 IoT Edge 部署的應用程式。

- **Azure Arc 命名空間** -您會連接到此 `azure-arc` 命名空間，以管理透過 Azure Arc 部署的應用程式。使用 Azure Arc，您也可以在其他使用者命名空間中部署應用程式。 

## <a name="namespaces-and-users"></a>命名空間和使用者

在真實世界中，請務必將叢集分割成多個命名空間。 

- **多個使用者**：如果您有多個使用者，則多個命名空間可讓這些使用者各自在其特定命名空間中部署其應用程式和服務。 
- **單一使用者**：即使有單一使用者，多個命名空間仍允許該使用者在相同的 Kubernetes 叢集中執行多個版本的應用程式。

### <a name="roles-and-rolebindings"></a>角色和 RoleBindings

Kubernetes 具有角色和角色系結的概念，可讓您將許可權授與命名空間層級和叢集層級的使用者或資源。 

- **角色**：您可以將許可權定義為 **角色** ，然後在命名空間中使用 **角色** 來授與許可權。 
- **RoleBindings**：定義角色之後，您可以使用 **RoleBindings** 指派指定命名空間的角色。 

這種方法可讓您以邏輯方式區隔單一 Kubernetes 叢集，讓使用者只能存取其指派命名空間中的應用程式資源。 

## <a name="rbac-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上的 RBAC

在目前的 RBAC 實施中，Azure Stack Edge Pro 可讓您從受限制的 PowerShell 執行時間採取下列動作：

- 建立命名空間。  
- 建立其他使用者。
- 授與您所建立之命名空間的系統管理員存取權。 請記住，您無法存取叢集系統管理員角色或整個叢集資源的觀點。
- 取得 `kubeconfig` 具有存取 Kubernetes 叢集資訊的檔案。


Azure Stack Edge Pro 裝置具有多個系統命名空間，而且您可以使用檔案來建立使用者命名空間， `kubeconfig` 以存取這些命名空間。 使用者可以完全掌控這些命名空間，也可以建立或修改使用者，或授與使用者存取權。 只有叢集系統管理員具有系統命名空間和整個叢集資源的完整存取權。 `aseuser`具有系統命名空間的唯讀存取權。

以下圖表描述 Azure Stack Edge Pro 裝置上的 RBAC 的執行。

![Azure Stack Edge Pro 裝置上的 RBAC](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

在此圖中，Alice、Bob 和 Chuck 只能存取指派的使用者命名空間，在此案例中為 `ns1` 、 `ns2` 和 `ns3` 分別為。 在這些命名空間中，它們具有系統管理員存取權。 另一方面，叢集系統管理員可以存取系統命名空間和整個叢集的資源。

您可以使用 `kubectl` 命令來建立命名空間和使用者、將使用者指派給命名空間，或下載 `kubeconfig` 檔。 以下是高層級的工作流程：

1. 建立命名空間和使用者。  

    `New-HcsKubernetesNamespace -Namespace`  

2. 建立使用者。  

    `New-HcsKubernetesUser -UserName`  

3. 將命名空間與您建立的使用者產生關聯。  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. 將使用者設定儲存至 `C:\Users\<username>\.kube` 。  

5. 安裝 `kubectl` 並開始將應用程式部署至 `kubectl` 。 

如需詳細的逐步指示，請移至 [Azure Stack Edge Pro 上的透過 Kuebctl 存取 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集。


使用 Azure Stack Edge Pro 裝置上的命名空間和使用者時，適用下列注意事項：

- 您不允許對任何系統命名空間執行任何作業，例如建立使用者、授與或撤銷使用者的命名空間存取權。 系統命名空間的範例包括、、、、 `kube-system` `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` 、 `kube-public` 。 系統命名空間也包含為部署類型保留的命名空間，例如 `iotedge` (IoT Edge 命名空間) 以及 `azure-arc` (Azure Arc 命名空間) 。
- 您可以建立使用者命名空間，並在這些命名空間內建立額外的使用者，並授與或撤銷這些使用者的命名空間存取權。
- 您不能建立任何名稱與任何系統命名空間相同的命名空間。 系統命名空間的名稱會保留。  
- 您無法使用其他使用者命名空間已使用的名稱來建立任何使用者命名空間。 例如，如果您已 `test-ns` 建立，就無法建立另一個 `test-ns` 命名空間。
- 您不能建立已保留名稱的使用者。 例如， `aseuser` 是保留的使用者，無法使用。


## <a name="next-steps"></a>下一步

若要瞭解如何建立使用者、建立命名空間，以及授與使用者命名空間的存取權，請參閱透過 [Kubectl 存取 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集。

