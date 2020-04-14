---
title: 常見問題集
description: 與 Azure 容器實例服務相關的常見問題的解答
author: dkkapur
ms.topic: article
ms.date: 04/10/2020
ms.openlocfilehash: 8730dcb24af61730d7f93ea37a53cf87435eb9f9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261613"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>有關 Azure 容器實例的常見問題

本文解決了有關 Azure 容器實例的常見問題。

## <a name="deployment"></a>部署

### <a name="how-large-can-my-container-image-be"></a>我的容器映射有多大?

Azure 容器實例上的可部署容器映射的最大大小為 15 GB。 您可以根據部署時的確切可用性部署更大的映射,但這不能保證。

容器映射的大小會影響部署所需的時間,因此通常希望使容器映射盡可能小。

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>如何加快容器的部署?

由於部署時間的主要決定因素之一是映射大小,因此請查找減小大小的方法。 刪除不需要的圖層,或減小圖像中的圖層大小(通過選取較輕的基本操作系統映射)。 例如,如果您正在運行 Linux 容器,請考慮使用阿爾卑斯作為基本映射,而不是完整的 Ubuntu 伺服器。 同樣,對於 Windows 容器,如果可能,請使用 Nano Server 基礎映射。 

還應檢查 Azure 容器映像中預快取映像的清單,這些映射可透過[清單緩存映射](/rest/api/container-instances/listcachedimages)API 提供。 您可能能夠切換出預快取影像之一的圖像圖層。 

請參考您的額外[資訊](container-instances-troubleshooting.md#container-takes-a-long-time-to-start)。

### <a name="what-windows-base-os-images-are-supported"></a>支援哪些 Windows 基本作業系統映射?

#### <a name="windows-server-2016-base-images"></a>Windows 伺服器 2016 基本映像

* [納米伺服器](https://hub.docker.com/_/microsoft-windows-nanoserver)`10.0.14393.x`: ,`sac2016`
* [Windows 伺服器核心](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,`10.0.14393.x`

> [!NOTE]
> 不支援基於半年頻道版本 1709 或 1803 的 Windows 映射。

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows 伺服器 2019 和用戶端基礎映像(預覽版)

* [奈米伺服器](https://hub.docker.com/_/microsoft-windows-nanoserver)`1809``10.0.17763.914`: 或更早
* [Windows 伺服器核心](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2019`:`1809``10.0.17763.914`、 、 或更早
* [視窗](https://hub.docker.com/_/microsoft-windows)`1809``10.0.17763.914`: 或更早

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>容器中應使用什麼 .NET 或 .NET 核心圖像層? 

使用滿足您要求的最小圖像。 對於 Linux,您可以使用*運行時-阿爾卑斯*.NET 核心映射,自 .NET Core 2.1 發佈以來,該映射一直受支援。 對於 Windows,如果您使用的是完整的 .NET 框架,則需要使用 Windows 伺服器核心映射(僅限運行時映射,如*4.7.2-windowsservercore-ltsc2016)。* 僅限執行時映像較小,但不支援需要 .NET SDK 的工作負載。

## <a name="availability-and-quotas"></a>可用性和配額

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>我應該為容器或容器組分配多少個內核和記憶體?

這真的取決於您的工作負載。 從小開始並測試性能,以查看容器的工作原理。 [監視 CPU 和記憶體資源使用方式](container-instances-monitor.md),然後根據在容器中部署的進程類型添加內核或記憶體。 

還請確保檢查要部署的區域[的資源可用性](container-instances-region-availability.md#availability---general),以造訪 CPU 內核上的邊界,以及每個容器組可用的記憶體。 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>ACI 運行哪些底層基礎結構?

Azure 容器實例旨在成為無伺服器容器按需服務,因此我們希望您專注於開發容器,而不必擔心基礎結構! 對於那些好奇或想要對性能進行比較的人來說,ACI 運行在各種 SKU 的 Azure VM 集上運行,主要來自 F 和 D 系列。 隨著我們不斷開發和優化服務,我們預計這種情況將在未來發生變化。 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>我想在 ACI 上部署數千個內核 - 是否可以增加配額?
 
是(有時)。 有關當前配額以及哪些限制可以通過請求增加,請參閱[配額和限制](container-instances-quotas.md)條款。

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>是否可以部署 4 個以上內核和 16 GB 的 RAM?

尚未提供。 目前,這些是容器組的最大值。 有關特定要求或請求,請與 Azure 支援部門聯繫。 

### <a name="when-will-aci-be-in-a-specific-region"></a>ACI 何時會位於特定區域?

當前區域可用性[在此處](container-instances-region-availability.md#availability---general)發佈。 如果對特定區域有要求,請與 Azure 支援部門聯繫。

## <a name="features-and-scenarios"></a>功能和案例

### <a name="how-do-i-scale-a-container-group"></a>如何縮放容器組?

目前,縮放不適用於容器或容器組。 如果您需要運行更多實例,請使用我們的 API 來自動執行並創建更多請求,以便向服務創建容器組。 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>在自定義 VNet 中運行的實例可以使用哪些功能?

可以在您選擇的[Azure 虛擬網路中部署容器組](container-instances-vnet.md),並將專用 IP 委派到容器組,以跨 Azure 資源在 VNet 中路由流量。 容器組部署到虛擬網路中目前可用於 Azure 區域子集中的生產工作負荷。

## <a name="pricing"></a>定價

### <a name="when-does-the-meter-start-running"></a>儀錶何時開始運行?

容器組持續時間從我們開始提取第一個容器映射(用於新部署)或重新啟動容器組(如果已部署)的時間計算,直到容器組停止。 請參考[容器實體的詳細資訊](https://azure.microsoft.com/pricing/details/container-instances/)。

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>停止容器時,我是否要停止充電?

停止整個容器組后,儀錶將停止運行。 只要容器組中的容器正在運行,我們就保留資源,以防您希望再次啟動容器。 

## <a name="next-steps"></a>後續步驟

* [瞭解有關](container-instances-overview.md)Azure 容器實例的更多詳細資訊。
* [解決](container-instances-troubleshooting.md)Azure 容器實例中的常見問題。