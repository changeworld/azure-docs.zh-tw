---
title: 常見問題集
description: Azure 容器實例服務相關常見問題的解答
author: macolso
ms.author: macolso
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1cedfb7464fd97249c5cd3fae73c9290365da6cd
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818490"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>關於 Azure 容器實例的常見問題

本文說明 Azure 容器實例的相關常見問題。

## <a name="deployment"></a>部署

### <a name="how-large-can-my-container-image-be"></a>我的容器映射可以有多大？

Azure 容器實例上可部署容器映射的大小上限為 15 GB。 您可以根據部署時的確切可用性來部署較大的映射，但這是不保證的。

容器映射的大小會影響部署所需的時間，因此通常您會想要盡可能將容器映射保持在最小。

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>如何加速容器的部署？

由於部署時間的其中一個主要強硬是映射大小，因此，請尋找縮減大小的方法。 移除不需要的圖層，或挑選較輕的基底 OS 映射) 來減少映射 (中的圖層大小。 例如，如果您執行的是 Linux 容器，請考慮使用 Alpine 做為您的基底映射，而不是完整的 Ubuntu Server。 同樣地，若為 Windows 容器，請盡可能使用 Nano Server 基礎映射。 

您也應該檢查 Azure 容器映射中預先快取的映射清單，這些映射可透過 [列出](/rest/api/container-instances/location/listcachedimages) 快取影像 API 取得。 您可能可以針對其中一個預先快取的影像切換出影像圖層。 

查看更多有關減少容器啟動時間的 [詳細指導](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) 方針。

### <a name="what-windows-base-os-images-are-supported"></a>支援哪些 Windows 基本 OS 映射？

> [!NOTE]
> 由於2020中的 Windows 更新後回溯相容性問題，以下映射版本包含我們建議您在基底映射中使用的最低版本號碼。 目前使用較舊映射版本的部署不會受到影響，但新的部署應遵守下列基底映射。 

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 基礎映射

* [Nano 伺服器](https://hub.docker.com/_/microsoft-windows-nanoserver)： `sac2016` `10.0.14393.3506` 或更新版本
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore)： `ltsc2016`  `10.0.14393.3506` 或更新版本

> [!NOTE]
> 不支援以半年通道版本1709或1803為基礎的 Windows 映像。

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 和用戶端基礎映射 (預覽) 

* [Nano 伺服器](https://hub.docker.com/_/microsoft-windows-nanoserver)： `1809` `10.0.17763.1040` 或更新版本
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore)： `ltsc2019` 、 `1809` `10.0.17763.1040` 或更新版本
* [Windows](https://hub.docker.com/_/microsoft-windows)： `1809` `10.0.17763.1040` 或更新版本

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>我應該在容器中使用什麼 .NET 或 .NET Core 映射層？ 

使用符合您需求的最小影像。 針對 Linux，您可以使用 *執行時間 alpine* 的 .net core 映射（自 .net core 2.1 版起已支援）。 若是 Windows，如果您使用完整 .NET Framework，則必須使用 Windows Server Core 映射 (僅限執行時間的映射，例如  *4.7.2-windowsservercore-ltsc2016*) 。 僅限執行時間的映射較小，但不支援需要 .NET SDK 的工作負載。

## <a name="availability-and-quotas"></a>可用性和配額

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>我應該為容器或容器群組配置多少核心和記憶體？

這真的取決於您的工作負載。 啟動小規模和測試效能，以查看容器的執行方式。 [監視 CPU 和記憶體資源使用量](container-instances-monitor.md)，然後根據您在容器中部署的進程類型來新增核心或記憶體。

請務必檢查您正在部署之區域的 [資源可用性](container-instances-region-availability.md#availability---general) ，以瞭解每個容器群組可用的 CPU 核心和可用記憶體上限。 

> [!NOTE]
> 服務的基礎結構會使用少量的容器群組資源。 您的容器將能存取大部分的資源，而不是所有配置給該群組的資源。 基於這個理由，在要求群組中容器的資源時，請規劃小型資源緩衝區。

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>ACI 有哪些基礎結構會執行？

Azure 容器實例的目標是無伺服器的容器隨選服務，因此我們希望您專注于開發容器，而不需要擔心基礎結構！ 對於對效能進行比較的好奇或想要進行比較的，ACI 是在各種 Sku 的一組 Azure Vm 上執行，主要來自 F 和 D 系列。 我們預期這項變更會在未來繼續開發及優化服務時變更。 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>我想要在 ACI 上部署上千個核心-我可以增加配額嗎？
 
是 (有時) 。 請參閱 [配額和限制](container-instances-quotas.md) 文章，以瞭解目前的配額，以及哪些限制可以依要求增加。

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>我可以使用超過4個核心和 16 GB 的 RAM 進行部署嗎？

目前還不行。 目前，這些是容器群組的最大上限。 請將 Azure 支援與特定需求或要求聯絡。 

### <a name="when-will-aci-be-in-a-specific-region"></a>ACI 何時會在特定區域中？

目前的區域可用性發佈 [位置](container-instances-region-availability.md#availability---general)。 如果您有特定區域的需求，請聯絡 Azure 支援。

## <a name="features-and-scenarios"></a>功能和案例

### <a name="how-do-i-scale-a-container-group"></a>如何? 調整容器群組的規模？

目前，無法對容器或容器群組使用調整。 如果您需要執行更多實例，請使用我們的 API 來自動化，並建立更多對服務建立容器群組的要求。 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>自訂 VNet 中執行的實例可使用哪些功能？

您可以在您選擇的 [azure 虛擬網路中部署容器群組](container-instances-vnet.md) ，並將私人 ip 委派給容器群組，以在 azure 資源之間路由傳送 VNet 內的流量。 如需 Azure 容器實例的網路案例和限制，請參閱 [虛擬網路案例和資源](container-instances-virtual-network-concepts.md)。

## <a name="pricing"></a>定價

### <a name="when-does-the-meter-start-running"></a>計量開始執行的時機為何？

容器群組持續時間的計算是從我們開始提取第一個容器的映射 (新的部署) 或您的容器群組重新開機 (如果已部署) ，直到容器群組停止為止。 請參閱 [容器實例定價](https://azure.microsoft.com/pricing/details/container-instances/)的詳細資料。

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>當容器停止時，是否會停止計費？

當整個容器群組停止後，計量會停止執行。 只要容器群組中的容器正在執行，我們就會保留資源，以防您想要重新開機容器。 

## <a name="next-steps"></a>下一步

* [深入瞭解](container-instances-overview.md) Azure 容器實例。
* 針對 Azure 容器實例中的[常見問題進行疑難排解](container-instances-troubleshooting.md)。
