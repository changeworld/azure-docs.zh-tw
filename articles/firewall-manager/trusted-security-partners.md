---
title: 什麼是 Azure 防火牆管理員信任的安全性合作夥伴（預覽）
description: 瞭解 Azure 防火牆管理員信任的安全性合作夥伴
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75436780"
---
# <a name="what-are-trusted-security-partners-preview"></a>什麼是信任的安全性合作夥伴 (預覽)？

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 防火牆管理員中的*信任安全性合作夥伴（預覽）* 可讓您使用熟悉、最適合的協力廠商安全性即服務（SECaaS）供應專案，來保護您的使用者的網際網路存取。

使用快速設定時，您可以使用支援的安全性合作夥伴來保護中樞，以及從您的虛擬網路（Vnet）或區域內的分支位置來路由和篩選網際網路流量。 這是使用自動化路由管理來完成的，而不需設定和管理使用者定義的路由（Udr）。

您可以在多個 Azure 區域中，使用您選擇的安全性合作夥伴來部署安全的中樞，以便在這些區域的全球各地取得使用者的連線能力和安全性。 透過使用安全性合作夥伴的供應專案來提供網際網路/SaaS 應用程式流量的功能，以及在安全中樞內用於私人流量的 Azure 防火牆，您現在可以開始在 Azure 上建立接近您全域散發的使用者和應用程式的安全性邊緣。

在此預覽中，支援的安全性合作夥伴為**ZScaler**和**iboss**。 支援的區域為 WestCentralUS、NorthCentralUS、WestUS、WestUS2 和 EastUS。

![信任的安全性合作夥伴](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>主要案例

在下列案例中，您可以使用安全性合作夥伴來篩選網際網路流量：

- 虛擬網路（VNet）到網際網路

   針對在 Azure 上執行的雲端工作負載，使用進階使用者感知網際網路保護。

- 分支到網際網路

   利用您的 Azure 連線能力和全域散發，輕鬆為分公司新增協力廠商 NSaaS 篩選。 您可以使用 Azure 虛擬 WAN 來建立全球傳輸網路和安全性邊緣。

以下是支援的案例：
-   透過協力廠商合作夥伴提供的 VNet 到網際網路。
-   透過協力廠商合作夥伴服務分支到網際網路。
-   透過 Azure 防火牆，透過協力廠商合作夥伴供應專案，將其餘的私用流量（輪輻、輪輻分支、分支到輪輻）分支到網際網路。

不支援下列案例：

- 透過合作夥伴供應專案對網際網路的 VNet 無法與 Azure 防火牆結合私人流量。 請參閱下列限制。

## <a name="current-limitations"></a>目前的限制

- 針對 VNet 對網際網路，您無法混合新增私人流量的 Azure 防火牆，以及網際網路流量的合作夥伴供應專案。 您可以將網際網路流量傳送至 Azure 防火牆或受保護虛擬中樞中的協力廠商安全性合作夥伴供應專案，但不能兩者都提供。 
- 您最多可以在每個虛擬中樞部署一個安全性合作夥伴。 如果您需要變更提供者，您必須移除現有的夥伴，並新增一個。

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>在受保護虛擬中樞中進行網際網路流量篩選的最佳作法

網際網路流量通常包含網路流量。 但它也包含以 SaaS 應用程式（例如 Office 365 （O365））和 Azure 公用 PaaS 服務（例如 Azure 儲存體、Azure Sql 等等）為目標的流量。 以下是處理這些服務之流量的最佳做法建議：

### <a name="handling-azure-paas-traffic"></a>處理 Azure PaaS 流量
 
- 如果您的流量大多包含 Azure PaaS，而且您應用程式的資源存取權可使用 IP 位址、Fqdn、服務標籤或 FQDN 標記進行篩選，請使用 Azure 防火牆進行保護。

- 如果您的流量是由 SaaS 應用程式存取所組成，或者您需要使用者感知篩選（例如，針對虛擬桌面基礎結構（VDI）工作負載），或需要先進的網際網路篩選功能，請在您的中樞使用協力廠商合作夥伴解決方案。

![Azure 防火牆管理員的所有案例](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>處理 Office 365 （O365）流量

在全域散發的分支位置案例中，您應該先將 Office 365 流量直接重新導向至分支，再將其餘的網際網路流量傳送至您的 Azure 安全中樞。

針對 Office 365，網路延遲和效能對於成功的使用者體驗很重要。 為了達到這些目標以獲得最佳效能和使用者體驗，客戶必須先實行 Office 365 直接和本機的 escape，再考慮透過 Azure 路由傳送網際網路流量的其餘部分。

[Office 365 網路連線原則](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles)會呼叫主要的 office 365 網路連線，以從使用者分支或行動裝置在本機進行路由傳送，並直接透過網際網路在最接近的 Microsoft 網路點上進行通訊。

此外，基於效能的考慮，Office 365 連線已針對隱私權和使用有效率的專屬通訊協定進行增強式加密。 這讓它不切合影響力，而是要將這些連線設為傳統網路層級安全性解決方案。 基於這些理由，我們強烈建議客戶在透過 Azure 傳送其餘流量之前，直接從分支傳送 Office 365 流量。 Microsoft 與數個與 Azure 和 Office 365 整合的 SD WAN 解決方案提供者合作，讓客戶能夠輕鬆地啟用 Office 365 direct 和當地網際網路的分類。 如需詳細資訊，請參閱[如何? 透過虛擬 WAN 設定我的 O365 原則？](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>後續步驟

[使用 Azure 防火牆管理員，在安全的中樞部署受信任的安全性](deploy-trusted-security-partner.md)供應專案。
