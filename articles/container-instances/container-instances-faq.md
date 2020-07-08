---
title: 常見問題集
description: Azure 容器實例服務相關常見問題的解答
author: dkkapur
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 21643ccfb6bb256e29114435ccb39a009d1b8dae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806596"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Azure 容器實例的相關常見問題

本文說明 Azure 容器實例的常見問題。

## <a name="deployment"></a>部署

### <a name="how-large-can-my-container-image-be"></a>我的容器映射可以有多大？

Azure 容器實例上可部署的容器映射大小上限為 15 GB。 您可能可以根據部署時的確切可用性來部署較大的映射，但不保證這點。

容器映射的大小會影響部署所需的時間，因此通常您會想要盡可能縮小容器映射。

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>如何加速容器的部署？

由於部署時間的其中一個主要 determinants 是影像大小，因此請尋找減少大小的方式。 移除您不需要的圖層，或減少映射中的圖層大小（藉由挑選較輕的基本 OS 映射）。 例如，如果您正在執行 Linux 容器，請考慮使用 Alpine 作為基底映射，而不是完整的 Ubuntu 伺服器。 同樣地，針對 Windows 容器，請盡可能使用 Nano Server 基底映射。 

您也應該在 Azure 容器映射中檢查預先快取的映射清單（可透過列出快取的[影像](/rest/api/container-instances/location/listcachedimages)API 取得）。 您可以針對其中一個預先快取的影像來切換影像圖層。 

請參閱減少容器啟動時間的詳細[指引](container-instances-troubleshooting.md#container-takes-a-long-time-to-start)。

### <a name="what-windows-base-os-images-are-supported"></a>支援哪些 Windows 基本 OS 映射？

> [!NOTE]
> 由於2020中 Windows 更新後的回溯相容性問題，下列映射版本會包含我們建議您在基底映射中使用的最小版本號碼。 使用較舊映射版本的目前部署不會受到影響，但新的部署應遵守下列基底映射。 

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 基底映射

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver)： `sac2016` `10.0.14393.3506` 或更新版本
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore)： `ltsc2016` `10.0.14393.3506` 或更新版本

> [!NOTE]
> 不支援以半年通道版本1709或1803為基礎的 Windows 映像。

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 和用戶端基底映射（預覽）

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver)： `1809` `10.0.17763.1040` 或更新版本
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore)： `ltsc2019` 、 `1809` `10.0.17763.1040` 或更新版本
* [Windows](https://hub.docker.com/_/microsoft-windows)： `1809` `10.0.17763.1040` 或更新版本

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>我應該在我的容器中使用什麼 .NET 或 .NET Core 映射層？ 

使用符合您需求的最小映射。 針對 Linux，您可以使用自 .NET Core 2.1 發行後所支援的*執行時間 alpine* .net Core 映射。 針對 Windows，如果您使用完整 .NET Framework，則必須使用 Windows Server Core 映射（僅限執行時間的映射，例如*4.7.2-windowsservercore-ltsc2016*）。 僅限執行時間的映射較小，但不支援需要 .NET SDK 的工作負載。

## <a name="availability-and-quotas"></a>可用性和配額

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>我應該為容器或容器群組配置多少核心和記憶體？

這實際上取決於您的工作負載。 從小規模開始，並測試效能以查看容器的執行方式。 [監視 CPU 和記憶體資源使用量](container-instances-monitor.md)，然後根據您在容器中部署的進程類型來新增核心或記憶體。

請務必檢查您要部署之區域的[資源可用性](container-instances-region-availability.md#availability---general)，以取得每個容器群組可用的 CPU 核心和記憶體上限。 

> [!NOTE]
> 服務的基礎結構會使用少量的容器群組資源。 您的容器將能夠存取所有配置給群組的資源，但並非全部。 基於這個理由，請在為群組中的容器要求資源時，規劃小型資源緩衝區。

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>ACI 執行所在的基礎結構為何？

Azure 容器實例的目標是無伺服器容器隨選服務，因此我們希望您可以專注于開發容器，而不必擔心基礎結構！ 對於想要對效能進行比較的人而言，ACI 會在各種 Sku 的 Azure Vm 集上執行，主要來自 F 和 D 系列。 我們預期這會在未來持續開發並優化服務時變更。 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>我想要在 ACI 上部署一千個核心-我可以增加配額嗎？
 
是（有時）。 請參閱[配額和限制](container-instances-quotas.md)一文，以瞭解目前的配額，以及可依要求增加哪些限制。

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>我可以使用4個以上的核心和 16 GB 的 RAM 來部署嗎？

目前還不行。 目前，這些是容器群組的最大上限。 使用特定需求或要求來聯絡 Azure 支援。 

### <a name="when-will-aci-be-in-a-specific-region"></a>ACI 何時會在特定區域中？

目前的區域可用性已在[此](container-instances-region-availability.md#availability---general)發佈。 如果您有特定區域的需求，請聯絡 Azure 支援。

## <a name="features-and-scenarios"></a>功能和案例

### <a name="how-do-i-scale-a-container-group"></a>如何? 調整容器群組的規模嗎？

目前，調整不適用於容器或容器群組。 如果您需要執行更多實例，請使用我們的 API 來自動化，並建立更多對服務建立容器群組的要求。 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>在自訂 VNet 中執行的實例可使用哪些功能？

您可以在您選擇的[Azure 虛擬網路中部署容器群組](container-instances-vnet.md)，並將私人 ip 委派給容器群組，以將 VNet 內的流量路由傳送到您的 azure 資源。 將容器群組部署至虛擬網路目前適用于 Azure 區域子集中的生產工作負載。

## <a name="pricing"></a>定價

### <a name="when-does-the-meter-start-running"></a>計量開始執行的時間為何？

容器群組持續時間是從我們開始提取第一個容器的映射（針對新的部署），或您的容器群組重新開機（如果已部署），直到容器群組停止為止的時間計算。 請參閱[容器實例定價](https://azure.microsoft.com/pricing/details/container-instances/)的詳細資料。

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>當我的容器停止時，是否要停止向我收費？

當整個容器群組停止後，計量就會停止執行。 只要容器群組中的容器正在執行，我們就會保存資源，以防您想要重新開機容器。 

## <a name="next-steps"></a>後續步驟

* [深入瞭解](container-instances-overview.md)Azure 容器實例。
* 針對 Azure 容器實例中[常見的問題進行疑難排解](container-instances-troubleshooting.md)。
