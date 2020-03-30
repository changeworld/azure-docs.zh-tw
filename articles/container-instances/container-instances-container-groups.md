---
title: 容器組簡介
description: 瞭解 Azure 容器實例中的容器組，這是共用生命週期的實例的集合以及存儲和網路等資源
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 73781418321c3932bf3e0190b646dcd3bb178195
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247211"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances 中的容器群組

在 Azure Container Instances 中，最上層的資源就是容器群組**。 本文說明容器群組為何，以及這些群組能夠實現的案例類型。

## <a name="what-is-a-container-group"></a>什麼是容器組？

容器群組是容器的集合，這些容器會排程在相同的主機電腦上。 容器組中的容器共用生命週期、資源、本地網路和存儲卷。 它的概念與[庫伯奈斯][kubernetes-pod]的*吊艙*相似。

下圖舉例說明包含多個容器的容器群組：

![容器群組圖表][container-groups-example]

此範例群組容器：

* 已排程在單一主機電腦上。
* 受指派 DNS 名稱標籤。
* 會公開單一的公用 IP 位址，以及一個公開的連接埠。
* 包括兩個容器。 一個容器接聽連接埠 80，另一個容器接聽連接埠 5000。
* 包含兩個 Azure 檔案共用作為磁碟區掛接，且每個容器會在本機掛接其中一個共用。

> [!NOTE]
> 多容器組目前僅支援 Linux 容器。 對於 Windows 容器，Azure 容器實例僅支援單個容器實例的部署。 當我們正在努力將所有功能帶到 Windows 容器時，您可以在服務[概述](container-instances-overview.md#linux-and-windows-containers)中找到當前平臺差異。

## <a name="deployment"></a>部署

以下是部署多容器組的兩種常見方法：使用[資源管理器範本][resource-manager template]或[YAML 檔][yaml-file]。 當您在部署容器實例時需要部署其他 Azure 服務資源（例如[，Azure 檔共用][azure-files]），建議使用資源管理器範本。 由於 YAML 格式的簡潔性，建議在部署僅包含容器實例時使用 YAML 檔。 有關可以設置的屬性的詳細資訊，請參閱[資源管理器範本引用](/azure/templates/microsoft.containerinstance/containergroups)或[YAML 參考](container-instances-reference-yaml.md)文檔。

要保留容器組的配置，可以使用 Azure CLI 命令[az 容器匯出][az-container-export]將設定匯出到 YAML 檔。 匯出允許您將容器組配置存儲在版本控制項中，以"以代碼身份配置"。 或者，在 YAML 中開發新組態時，您可以使用匯出的檔案作為起點。



## <a name="resource-allocation"></a>資源配置

Azure 容器實例通過添加組中實例[的資源請求][resource-requests]，將 CPU、記憶體和可選[GPU（][gpus]預覽）等資源配置給多容器組。 以 CPU 資源為例，如果創建一個包含兩個容器實例的容器組，每個容器組請求 1 個 CPU，則分配容器組 2 個 CPU。

### <a name="resource-usage-by-container-instances"></a>按容器實例進行的資源使用

組中的每個容器實例都分配其資源請求中指定的資源。 但是，如果配置其可選[資源限制][resource-limits]屬性，則組中容器實例使用的最大資源可能不同。 容器實例的資源限制必須大於或等於強制[資源請求][resource-requests]屬性。

* 如果不指定資源限制，容器實例的最大資源使用量與其資源請求相同。

* 如果為容器實例指定限制，則實例的最大使用量可能大於請求，最高可達您設置的限制。 相應地，組中其他容器實例的資源使用量可能會減少。 可以為容器實例設置的最大資源限制是分配給組的總資源。
    
例如，在具有兩個容器實例的組中，每個容器請求 1 個 CPU，其中一個容器可能會運行比運行更多的 CPU 的工作負載。

在這種情況下，您可以為容器實例設置 2 個 CPU 的資源限制。 此配置允許容器實例使用最多 2 個 CPU（如果可用）。

### <a name="minimum-and-maximum-allocation"></a>最小和最大分配

* **至少**為容器組分配 1 個 CPU 和 1 GB 的記憶體。 組中的各個容器實例的記憶體少於 1 個 CPU 和 1 GB 的記憶體。 

* 有關容器組中**的最大**資源，請參閱部署區域中的 Azure 容器實例的資源[可用性][region-availability]。

## <a name="networking"></a>網路

容器組可以共用面向外部的 IP 位址、該 IP 位址上的一個或多個埠以及具有完全限定功能變數名稱 （FQDN） 的 DNS 標籤。 若要讓外部用戶端能夠連線到該群組內的容器，您必須從該容器公開該 IP 位址上的連接埠。 由於組中的容器共用埠命名空間，因此不支援埠映射。 刪除容器組時，將釋放容器組的 IP 位址和 FQDN。 

在容器組中，容器實例可以通過任何埠上的本地主機相互到達，即使這些埠未在組 IP 位址或容器外部公開也是如此。

可選地將容器組部署到[Azure 虛擬網路][virtual-network]，以允許容器與虛擬網路中的其他資源安全地通信。

## <a name="storage"></a>存放裝置

您可以指定要在容器群組內掛接的外部磁碟區。 支援的卷包括：
* [Azure 檔案共用][azure-files]
* [秘密][secret]
* [空目錄][empty-directory]
* [克隆 git 存儲庫][volume-gitrepo]

您可以將這些磁碟區對應到群組之個別容器內的特定路徑。 

## <a name="common-scenarios"></a>常見的案例

如果您想要將單一功能性工作分割成少量的容器映像，多個容器的群組會很實用。 然後，這些映像就可以由不同小組傳遞，且具有個別的資源需求。

使用範例可能包括：

* 一個容器提供 Web 應用程式，一個容器從原始檔控制提取最新內容。
* 一個應用程式容器和一個記錄容器。 記錄容器收集主應用程式所輸出的記錄和計量，並將這些資料寫入到長期存放區。
* 一個應用程式容器和一個監視容器。 監視容器會定期向應用程式發出要求，以確保它會保持執行狀態並正確回應，如果沒有正確回應則引發警示。
* 前端容器和後端容器。 前端可能為 Web 應用程式提供服務，後端運行服務以檢索資料。 

## <a name="next-steps"></a>後續步驟

了解如何使用 Azure Resource Manager 範本來部署多個容器的容器群組：

> [!div class="nextstepaction"]
> [部署容器群組][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
