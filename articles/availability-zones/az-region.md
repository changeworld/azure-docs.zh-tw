---
title: 在 Azure 中支援可用性區域的區域
description: 若要在 Azure 中建立高度可用且具有恢復功能的應用程式，可用性區域可提供可讓您用來執行資源的實體不同位置。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/05/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: cf1fc81ea63db21d2e864c00e1987eec3d376b59
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853155"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>在 Azure 中支援可用性區域的區域

「可用性區域」是高可用性供應項目，可保護您的應用程式和資料不受資料中心故障影響。 如需可用性區域的詳細資訊，請參閱[Azure 中的區域和可用性區域](az-overview.md)。

本節列出支援可用性區域的 Azure 服務和區域。

您可以在[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)中找到每個區域中可用的服務，以及即將推出的可用性藍圖。

## <a name="americas"></a>美洲

| 服務 | 美國中部 | 美國東部 | 美國東部 2 | 美國西部 2 |
| --- | :---: | :---: | :---: | :---: |
| **計算** |  |  |  |  |
| Linux 虛擬機器             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows 虛擬機器           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 虛擬機器擴展集         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service 環境 ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| 受控磁碟                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 區域-多餘儲存體             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **網路功能** |  |  |  |  |
| 標準 IP 位址                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN 閘道                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute 閘道               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 應用程式閘道 (V2)            | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure 防火牆                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **資料庫** |  |  |  |  |
| Azure 資料總管                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | ： heavy_check_mark： (預覽)  |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |  |
| 事件中樞                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **整合** |  |  |  |  |
| 服務匯流排 (僅限進階層)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **身分識別** |  |  |  |  |
| Azure AD 網域服務           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="europe"></a>歐洲

| 服務 | 法國中部 | 歐洲北部 | 英國南部 | 西歐 |
| --- | :---: | :---: | :---: | :---: |
| **計算** |  |  |  |  |
| Linux 虛擬機器             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows 虛擬機器           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 虛擬機器擴展集         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service 環境 ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| 受控磁碟                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 區域-多餘儲存體             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **網路功能** |  |  |  |  |
| 標準 IP 位址                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN 閘道                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute 閘道               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 應用程式閘道 (V2)            | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure 防火牆                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **資料庫** |  |  |  |  |
| Azure 資料總管                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL Database                       | :heavy_check_mark: | ： heavy_check_mark： (預覽)  | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |  |
| 事件中樞                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **整合**  |  |  |  |  |
| 服務匯流排 (僅限進階層)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **身分識別** |  |  |  |  |
| Azure AD 網域服務           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="asia-pacific"></a>亞太地區

| 服務 | 日本東部 | 東南亞 | 澳大利亞東部 |
| --- | :---: | :---: | :---: |
| **計算** |  |  |  |
| Linux 虛擬機器             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows 虛擬機器           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 虛擬機器擴展集         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service 環境 ILB | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: |  |
| **Storage** |  |  |  |
| 受控磁碟                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 區域-多餘儲存體             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **網路功能** |  |  |  |
| 標準 IP 位址                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN 閘道                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute 閘道               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 應用程式閘道 (V2)            | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure 防火牆                     | :heavy_check_mark: | :heavy_check_mark: |  |
| **資料庫** |  |  |  |
| Azure 資料總管                | :heavy_check_mark: | :heavy_check_mark: |  |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |
| 事件中樞                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **整合** |  |  |  |
| 服務匯流排 (僅限進階層)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: |  |
| **身分識別** |  |  |  |
| Azure AD 網域服務           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>其他

Azure 也在下欄區域提供可用性區域支援：

- US Gov 維吉尼亞州
- 南非北部
- 美國中南部
- 加拿大中部

若要深入瞭解這四個區域中的可用性區域支援，請洽詢您的 Microsoft 銷售或客戶代表，或開啟技術支援要求。

## <a name="next-steps"></a>後續步驟

- [Azure 中的區域和可用性區域](az-overview.md) \(部分機器翻譯\)
