---
title: Azure 中的路由喜好設定
description: 了解如何使用路由喜好設定來選擇 Azure 與網際網路之間的流量路由。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 0ae06a1c3d486b5d5998b4c6d050d86f50910a0a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595547"
---
# <a name="what-is-routing-preference-preview"></a>什麼是路由喜好設定 (預覽)？

Azure 路由喜好設定可讓您選擇流量在 Azure 與網際網路之間的路由方式。 您可以選擇透過 Microsoft 網路或經由 ISP 網路 (公用網際網路) 來路由傳送流量。 這些選項也分別稱為「冷馬鈴薯路由」和「熱馬鈴薯路由」。 輸出資料傳輸價格會根據選取的路由而有所不同。 您可以在建立公用 IP 位址時選擇路由選項。 公用 IP 位址可以與資源 (例如虛擬機器、虛擬機器擴展集、網際網路對向負載平衡器等) 相關聯。您也可以設定 Azure 儲存體資源的路由喜好設定，例如 Blob、檔案、網頁和 Azure DataLake)。 根據預設，所有 Azure 服務的流量會透過 Microsoft 全球網路路由傳送。

> [!IMPORTANT]
> 路由喜好設定目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="routing-via-microsoft-global-network"></a>透過 Microsoft 全球網路路由傳送

透過「Microsoft 全域網路」來路由傳送流量時，會透過全球最大的網路之一來傳遞流量，其跨越 160,000 英里的光纖，並具有超過 165 個邊緣存在點 (POP)。 網路已妥善佈建多個重複的光纖路徑，以確保具有極高的可靠性和可用性。 流量工程由軟體定義的 WAN 控制器管理，可確保選取流量的低延遲路徑，並提供進階的網路效能。

![透過 Microsoft 全球網路路由傳送](media/routing-preference-overview/route-via-microsoft-global-network.png)

**輸入流量：** 全球 BGP Anycast 公告可確保輸入流量進入最接近使用者的 Microsoft 網路。 例如，如果來自新加坡的使用者存取在美國芝加哥裝載的 Azure 資源，則會在新加坡 Edge POP 中將流量輸入至 Microsoft 全球網路，並在 Microsoft 網路上傳送到在芝加哥裝載的服務。

**輸出流量：** 輸出流量會遵循相同的原則。 大部份的流量會在 Microsoft 全球網路上移動，並在最接近使用者的網路結束。 例如，如果來自 Azure 芝加哥的流量是以來自新加坡的使用者為目標，則流量會在 Microsoft 網路上從芝加哥前往新加坡，並在新加坡 Edge POP 中結束 Microsoft 網路。

輸入和輸出流量都會在 Microsoft 全球網路上大量傳送。 這也稱為「冷馬鈴薯路由」。


## <a name="routing-over-public-internet-isp-network"></a>透過公用網際網路路由傳送 (ISP 網路)

新的「網際網路路由」路由選擇會將 Microsoft 全球網路上的流量降到最低，並使用傳輸 ISP 網路來路由傳送您的流量。 這個成本最佳化路由選項可提供與其他雲端提供者類似的網路效能。

![透過公用網際網路路由傳送](media/routing-preference-overview/route-via-isp-network.png)

**輸入流量：** 輸入路徑會使用「熱馬鈴薯路由」，表示流量會進入最接近已裝載服務區域的 Microsoft 網路。 例如，如果來自新加坡的使用者存取芝加哥裝載的 Azure 資源，則流量會透過公用網際網路傳送，並進入芝加哥的 Microsoft 全球網路。

**輸出流量：** 輸出流量會遵循相同的原則。 流量會在裝載服務的相同區域中結束 Microsoft 網路。 例如，如果來自 Azure 芝加哥服務的流量是以來自新加坡的使用者為目標，則流量會在芝加哥結束 Microsoft 網路，並透過公用網際網路傳送至在新加坡的使用者。

## <a name="supported-services"></a>支援的服務

選擇「Microsoft 全球網路」為路由喜好設定的公用 IP 可與任何 Azure 服務相關聯。 不過，選擇**網際網路**為路由喜好設定的公用 IP 可與下列 Azure 資源建立關聯：

* 虛擬機器
* 虛擬機器擴展集
* Azure Kubernetes Service (AKS)
* 網際網路對應負載平衡器
* 應用程式閘道
* Azure 防火牆

針對儲存體，主要端點一律會使用 **Microsoft 全球網路**。 您可以使用**網際網路**來啟用次要端點，以作為您的流量路由選擇。 以下為支援的儲存體服務：

* Blob
* 檔案
* Web
* Azure DataLake

## <a name="pricing"></a>定價
這兩個選項之間的價格差異會反映在網際網路輸出資料傳輸定價中。 透過 **Microsoft 全球網路**進行路由傳送的資料傳輸價格與目前的網際網路輸出價格相同。 如需最新的定價資訊，請造訪 [Azure 頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)。 透過**公用網際網路**進行路由傳送的定價如下表所示：

| 輸出來源區域 | 0-5 GB/月 | 5GB-10 TB/月 | 10-50 TB/月 | 50-150 TB/月 | 150-500 TB/月 |
| --- | --- | --- | --- | --- | --- |
| 區域 1 | $0/GB | $0.085/GB | $0.065/GB | $0.06/GB | $0.04/GB |
| 區域 2 | $0/GB | $0.11/GB | $0.075/GB | $0.07/GB | $0.06/GB  |

若每月超過 500 TB 磁碟區，[請與我們連絡](https://azure.microsoft.com/overview/sales-number/)。
* 區域 1 - 澳大利亞中部、澳大利亞中部 2、加拿大中部、加拿大東部、北歐、西歐、法國中部、法國南部、德國北部 (公開)、德國中西部 (公開)、挪威東部、挪威西部、瑞士北部、瑞士西部、英國南部、英國西部、美國中部、美國東部、美國東部 2、美國中北部、美國中南部、美國西部、美國西部 2 和美國中西部。

* 區域 2 - 東亞、東南亞、澳大利亞東部、澳大利亞東南部、印度中部、印度南部、印度西部、日本東部、日本西部、南韓中部及南韓南部。

* 區域 3 - 巴西南部、南非北部、南非西部、阿拉伯聯合大公國中部和阿拉伯聯合大公國北部。

## <a name="availability"></a>可用性

使用公用 IP 進行網際網路輸出的路由喜好設定支援適用於下列區域的服務 - 北歐、西歐、法國南部、英國南部、美國東部、美國中北部、美國中南部、美國西部、美國中西部、東南亞、德國中西部、瑞士西部、日本東部和日本西部。

儲存體帳戶的路由喜好設定支援適用於下列 Azure 區域：法國南部、美國中北部和美國中西部。
## <a name="limitations"></a>限制

* 路由喜好設定僅與公用 IP 位址的標準 SKU 相容。 不支援公用 IP 位址的基本 SKU。
* 路由喜好設定目前僅支援 IPV4 公用 IP 位址。 不支援 IPv6 公用 IP 位址。
* 具有多個 NIC 的虛擬機器只能有一種類型的路由喜好設定。


## <a name="next-steps"></a>後續步驟

* [使用 Azure PowerShell 設定 VM 的路由喜好設定](configure-routing-preference-virtual-machine-powershell.md)
* [使用 Azure CLI 設定 VM 的路由喜好設定](configure-routing-preference-virtual-machine-cli.md)
