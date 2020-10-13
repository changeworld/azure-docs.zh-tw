---
title: 什麼是 Azure 防火牆管理員安全性合作夥伴提供者？
description: 瞭解 Azure 防火牆管理員安全性合作夥伴提供者
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 3c09e75a98e5342ab54710e05466755d86705b2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399426"
---
# <a name="what-are-security-partner-providers"></a>什麼是安全性合作夥伴提供者？

Azure 防火牆管理員中的*安全性合作夥伴提供者*，可讓您使用熟悉、最知名的協力廠商安全性即服務 (SECaaS) 供應專案，以保護使用者的網際網路存取。

使用快速設定時，您可以使用支援的安全性合作夥伴來保護中樞，並從虛擬網路路由傳送和篩選網際網路流量， (Vnet) 或區域內的分支位置。 您可以使用自動化路由管理來達成此目的，而不需要設定及管理使用者定義的路由， (Udr) 。

您可以在多個 Azure 區域中，部署以您選擇的安全性合作夥伴設定的安全中樞，以取得這些區域全球各地使用者的連線能力和安全性。 您現在可以在受保護的中樞內使用安全性合作夥伴提供的網際網路/SaaS 應用程式流量，以及適用于私用流量的 Azure 防火牆，您現在可以在 Azure 上開始建立接近全球散發的使用者和應用程式的安全性邊緣。

支援的安全性合作夥伴為 **ZScaler**、 **Check Point** (preview) ，以及 **iboss** (preview) 。

![安全性合作夥伴提供者](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>主要案例

在下列案例中，您可以使用安全性合作夥伴來篩選網際網路流量：

- 虛擬網路 (VNet) 至網際網路

   針對在 Azure 上執行的雲端工作負載，使用進階使用者感知網際網路保護。

- 分支至網際網路

   運用您的 Azure 連線能力和全球散發，輕鬆地為分支至網際網路案例新增協力廠商 NSaaS 篩選。 您可以使用 Azure 虛擬 WAN 來建立全球傳輸網路和安全性邊緣。

以下是支援的案例：
- 透過安全性合作夥伴提供者的 VNet/分支至網際網路，以及透過 Azure 防火牆 (輪輻、輪輻到分支、分支至輪輻) 的其他流量。
- 透過安全性合作夥伴提供者的 VNet/分支至網際網路

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>安全虛擬中樞內網際網路流量篩選的最佳作法

網際網路流量通常包含網路流量。 但它也包含以 SaaS 應用程式（例如 Microsoft 365 和 Azure 公用 PaaS 服務，例如 Azure 儲存體、Azure Sql 等）為目標的流量。 以下是處理這些服務流量的最佳作法建議：

### <a name="handling-azure-paas-traffic"></a>處理 Azure PaaS 流量
 
- 如果您的流量大部分都是由 Azure PaaS 所組成，且您應用程式的資源存取權可以使用 IP 位址、Fqdn、服務標籤或 FQDN 標籤進行篩選，請使用 Azure 防火牆進行保護。

- 如果您的流量包含 SaaS 應用程式存取，或需要使用者感知篩選 (例如，您的虛擬桌面基礎結構 (VDI) 工作負載) 或需要先進的網際網路篩選功能，請在您的中樞內使用協力廠商合作夥伴解決方案。

![Azure 防火牆管理員的所有案例](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>處理 Microsoft 365 流量

在全域散發的分支位置案例中，您應該在傳送 Azure 安全中樞的剩餘網際網路流量之前，直接在分支重新導向 Microsoft 365 流量。

針對 Microsoft 365，網路延遲和效能對於成功的使用者體驗很重要。 為了達到最佳效能和使用者體驗的這些目標，客戶必須先實施 Microsoft 365 直接和本機的宣告，再考慮透過 Azure 路由傳送其餘的網際網路流量。

[Microsoft 365 網路連線原則](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles) 呼叫，以從使用者分支或行動裝置在本機路由傳送金鑰 Microsoft 365 網路連線，並直接透過網際網路連線到最接近的 Microsoft 網路點。

此外，Microsoft 365 連接會針對隱私權進行加密，並基於效能考慮，使用有效率的專屬通訊協定。 這使得這些連線與傳統網路層級安全性解決方案的使用方式並不切合具影響力。 基於這些原因，我們強烈建議客戶直接從分支傳送 Microsoft 365 流量，再透過 Azure 傳送其餘的流量。 Microsoft 已與數個 SD WAN 解決方案提供者合作，這些提供者會與 Azure 整合並 Microsoft 365，讓客戶能夠輕鬆地啟用 Microsoft 365 直接和本機網際網路的分類。 如需詳細資訊，請參閱 [什麼是 Azure 虛擬 WAN？](../virtual-wan/virtual-wan-about.md)

## <a name="next-steps"></a>後續步驟

[使用 Azure 防火牆管理員，在安全的中樞部署安全性合作夥伴](deploy-trusted-security-partner.md)供應專案。
