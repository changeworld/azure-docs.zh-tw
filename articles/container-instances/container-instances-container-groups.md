---
title: 容器群組簡介
description: 瞭解 Azure 容器實例中的容器群組，這是共用生命週期和資源（例如 Cpu、儲存體和網路）的實例集合。
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 72ebe6186da179bc5a1effddcc14327455eb7557
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89612416"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances 中的容器群組

在 Azure Container Instances 中，最上層的資源就是容器群組**。 本文說明容器群組為何，以及這些群組能夠實現的案例類型。

## <a name="what-is-a-container-group"></a>什麼是容器群組？

容器群組是容器的集合，這些容器會排程在相同的主機電腦上。 容器群組中的容器會共用生命週期、資源、區域網路和存放磁片區。 它與[Kubernetes][kubernetes-pod]中的*pod*概念類似。

下圖舉例說明包含多個容器的容器群組：

![容器群組圖表][container-groups-example]

此範例群組容器：

* 已排程在單一主機電腦上。
* 受指派 DNS 名稱標籤。
* 會公開單一的公用 IP 位址，以及一個公開的連接埠。
* 包括兩個容器。 一個容器接聽連接埠 80，另一個容器接聽連接埠 5000。
* 包含兩個 Azure 檔案共用作為磁碟區掛接，且每個容器會在本機掛接其中一個共用。

> [!NOTE]
> 多容器群組目前僅支援 Linux 容器。 針對 Windows 容器，Azure 容器實例僅支援單一容器實例的部署。 當我們致力於將所有功能帶入 Windows 容器時，您可以在服務 [總覽](container-instances-overview.md#linux-and-windows-containers)中找到目前的平臺差異。

## <a name="deployment"></a>部署

以下是部署多容器群組的兩個常見方式：使用 [Resource Manager 範本][resource-manager template] 或 [YAML][yaml-file]檔案。 當您需要部署額外的 Azure 服務資源時，建議使用 Resource Manager 範本 (例如，當您部署容器實例時， [Azure 檔案儲存體共用][azure-files]) 。 由於 YAML 格式較簡潔，因此當您的部署只包含容器實例時，建議使用 YAML 檔案。 如需您可以設定之屬性的詳細資訊，請參閱 [Resource Manager 範本參考](/azure/templates/microsoft.containerinstance/containergroups) 或 [YAML 參考](container-instances-reference-yaml.md) 檔。

若要保留容器群組的設定，您可以使用 Azure CLI 命令 [az 容器匯出][az-container-export]將設定匯出至 YAML 檔案。 匯出可讓您將容器群組設定儲存在「設定即程式碼」的版本控制中。 或者，在 YAML 中開發新組態時，您可以使用匯出的檔案作為起點。



## <a name="resource-allocation"></a>資源配置

Azure 容器實例會將資源[要求][resource-requests]新增到群組中，以將 cpu、記憶體和選擇性[gpu][gpus] (預覽) 的資源配置給多容器群組。 以 CPU 資源為例，如果您建立具有兩個容器實例的容器群組，每個容器實例都要求1個 CPU，則會配置2個 cpu 給容器群組。

### <a name="resource-usage-by-container-instances"></a>容器實例的資源使用量

群組中的每個容器實例都會配置其資源要求中指定的資源。 但是，如果您設定其選擇性的 [資源限制][resource-limits] 屬性，群組中的容器實例所使用的資源上限可能會不同。 容器實例的資源限制必須大於或等於強制 [資源要求][resource-requests] 屬性。

* 如果您未指定資源限制，容器實例的最大資源使用量會與其資源要求相同。

* 如果您指定容器實例的限制，實例的最大使用量可能會大於要求，直到您設定的限制為止。 同樣地，群組中其他容器實例的資源使用量可能會降低。 您可以為容器實例設定的資源限制上限是配置給群組的資源總數。
    
例如，在有兩個容器實例的群組中，每個容器實例都要求1個 CPU，您的其中一個容器可能會執行需要更多 Cpu 才能執行的工作負載。

在此案例中，您可以為容器實例設定最多2個 Cpu 的資源限制。 此設定可讓容器實例使用最多2個 Cpu （如果有的話）。

> [!NOTE]
> 服務的基礎結構會使用少量的容器群組資源。 您的容器將能存取大部分的資源，而不是所有配置給該群組的資源。 基於這個理由，在要求群組中容器的資源時，請規劃小型資源緩衝區。

### <a name="minimum-and-maximum-allocation"></a>最小和最大配置

* 為容器群組配置 **最少** 1 個 CPU 和 1 GB 的記憶體。 群組內的個別容器實例可以布建小於1個 CPU 和 1 GB 的記憶體。 

* 如需容器群組中的資源 **上限** ，請參閱部署區域中 Azure 容器實例的 [資源可用性][region-availability] 。

## <a name="networking"></a>網路功能

容器群組可以共用對外 IP 位址、該 IP 位址上的一或多個埠，以及具有完整功能變數名稱的 DNS 標籤， (FQDN) 。 若要讓外部用戶端能夠連線到該群組內的容器，您必須從該容器公開該 IP 位址上的連接埠。 刪除容器群組時，會釋放容器群組的 IP 位址和 FQDN。 

在容器群組內，容器實例可透過任何埠上的 localhost 彼此連線，即使這些埠未在群組的 IP 位址或容器中對外公開。

（選擇性）將容器群組部署至 [Azure 虛擬網路][virtual-network] ，以允許容器與虛擬網路中的其他資源安全地通訊。

## <a name="storage"></a>儲存體

您可以指定要在容器群組內掛接的外部磁碟區。 支援的磁片區包括：
* [Azure 檔案共用][azure-files]
* [祕密][secret]
* [空目錄][empty-directory]
* [複製的 git 存放庫][volume-gitrepo]

您可以將這些磁碟區對應到群組之個別容器內的特定路徑。 

## <a name="common-scenarios"></a>常見的案例

如果您想要將單一功能性工作分割成少量的容器映像，多個容器的群組會很實用。 然後，這些映像就可以由不同小組傳遞，且具有個別的資源需求。

使用範例可能包括：

* 一個容器提供 Web 應用程式，一個容器從原始檔控制提取最新內容。
* 一個應用程式容器和一個記錄容器。 記錄容器收集主應用程式所輸出的記錄和計量，並將這些資料寫入到長期存放區。
* 一個應用程式容器和一個監視容器。 監視容器會定期向應用程式發出要求，以確保它會保持執行狀態並正確回應，如果沒有正確回應則引發警示。
* 前端容器和後端容器。 前端可能會提供 web 應用程式，並執行服務的後端來取出資料。 

## <a name="next-steps"></a>後續步驟

了解如何使用 Azure Resource Manager 範本來部署多個容器的容器群組：

> [!div class="nextstepaction"]
> [部署容器群組][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-virtual-network-concepts.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
