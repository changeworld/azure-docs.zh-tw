---
title: 什麼是 Azure 防火牆管理器受信任的安全合作夥伴（預覽版）
description: 瞭解 Azure 防火牆管理器受信任的安全合作夥伴
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436780"
---
# <a name="what-are-trusted-security-partners-preview"></a>什麼是信任的安全性合作夥伴 (預覽)？

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 防火牆管理器中的*可信安全合作夥伴（預覽版）* 允許您使用您熟悉的、同類最佳的協力廠商安全作為服務 （SECaaS） 產品來保護使用者的 Internet 訪問。

通過快速配置，您可以使用受支援的安全合作夥伴保護集線器，並從虛擬網路 （VNet） 或區域內的分支位置路由和篩選 Internet 流量。 這是使用自動路由管理完成的，無需設置和管理使用者定義的路由 （UDR）。

您可以在多個 Azure 區域中部署由您選擇的安全合作夥伴配置的安全集線器，以便這些區域中的全球任何地方的使用者獲得連接和安全。 借助使用安全合作夥伴的 Internet/SaaS 應用程式流量產品，以及安全集線器中專用流量的 Azure 防火牆，現在可以開始在接近全球分散式使用者和應用程式的 Azure 上構建安全邊緣。

對於此預覽，支援的安全合作夥伴是**ZScaler**和**iboss**。 支援的區域是西中環、北中美、西US、西US2和東US。

![信任的安全性合作夥伴](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>主要案例

在以下情況下，您可以使用安全合作夥伴來篩選 Internet 流量：

- 虛擬網路 （VNet） 到互聯網

   針對在 Azure 上執行的雲端工作負載，使用進階使用者感知網際網路保護。

- 互聯網分支

   利用 Azure 連接和全域分發，輕鬆將協力廠商 NSaaS 篩選添加到 Internet 方案中的分支。 您可以使用 Azure 虛擬 WAN 構建全域傳輸網路和安全邊緣。

以下是支援的案例：
-   通過協力廠商合作夥伴產品向互聯網提供 VNet。
-   通過協力廠商合作夥伴產品向互聯網分支機搆。
-   通過協力廠商合作夥伴產品分支到 Internet，其餘通過 Azure 防火牆進行專用流量（從分支到分支、分支到分支、分支到分支）。

不支援以下方案：

- 通過合作夥伴產品向 Internet 提供的 VNet 不能與用於專用流量的 Azure 防火牆組合使用。 請參閱以下限制。

## <a name="current-limitations"></a>目前的限制

- 對於 VNet 到 Internet，不能混合添加用於專用流量的 Azure 防火牆和用於 Internet 流量的合作夥伴產品。 您可以將 Internet 流量發送到 Azure 防火牆或安全虛擬中心中的協力廠商安全合作夥伴產品，但不能同時發送這兩者。 
- 每個虛擬中心最多隻能部署一個安全夥伴。 如果需要更改提供程式，則必須刪除現有合作夥伴並添加新合作夥伴。

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>安全虛擬集線器中互聯網流量過濾的最佳做法

互聯網流量通常包括 Web 流量。 但它也包括發送到 SaaS 應用程式（如 Office 365 （O365） 和 Azure 公共 PaaS 服務（如 Azure 存儲、Azure Sql 等）的流量。 以下是處理這些服務流量的最佳做法建議：

### <a name="handling-azure-paas-traffic"></a>處理 Azure PaaS 流量
 
- 如果流量主要由 Azure PaaS 組成，並且可以使用 IP 位址、FQDN、服務標記或 FQDN 標記篩選應用程式的資源訪問，請使用 Azure 防火牆進行保護。

- 如果您的流量由 SaaS 應用程式訪問組成，或者您需要使用者感知篩選（例如，對於虛擬桌面基礎結構 （VDI） 工作負載），或者您需要高級 Internet 篩選功能，請使用集線器中的協力廠商合作夥伴解決方案。

![Azure 防火牆管理器的所有方案](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>處理 Office 365 （O365） 流量

在全球分佈的分支位置方案中，在發送 Azure 安全集線器的其餘 Internet 流量之前，應直接重定向分支的 Office 365 流量。

對於 Office 365，網路延遲和性能對於成功的使用者體驗至關重要。 為了圍繞最佳性能和使用者體驗實現這些目標，客戶在考慮通過 Azure 路由其餘 Internet 流量之前，必須實現 Office 365 直接和本地轉義。

[Office 365 網路連接原則](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles)要求從使用者分支或行動裝置本地路由關鍵 Office 365 網路連接，然後直接通過 Internet 路由到最近的 Microsoft 網路存在點。

此外，Office 365 連接出於隱私而進行增強式加密，出於性能原因使用高效的專有協定。 這使得將這些連接置於傳統的網路安全解決方案上是不切實際的和有影響的。 出於這些原因，我們強烈建議客戶直接從分支機搆發送 Office 365 流量，然後再通過 Azure 發送其餘流量。 Microsoft 與多個 SD-WAN 解決方案供應商合作，這些供應商與 Azure 和 Office 365 集成，使客戶能夠輕鬆啟用 Office 365 直接和本地 Internet 突破。 有關詳細資訊，請參閱[如何通過虛擬 WAN 設置我的 O365 策略？](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>後續步驟

[使用 Azure 防火牆管理器 在安全中心部署受信任的安全產品](deploy-trusted-security-partner.md)。
