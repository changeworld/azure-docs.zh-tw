---
title: Azure 資訊安全中心的定價
description: 在是否包含 Azure Defender 的基礎下，Azure 資訊安全中心分成兩種模式。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: c5537552e35c1738e6c3cd4873b4c6444685a5fe
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448309"
---
# <a name="pricing-of-azure-security-center"></a>Azure 資訊安全中心的定價
Azure 資訊安全中心提供統一的安全性管理和進階的威脅防護，保護 Azure、內部部署和其他雲端中執行的工作負載。 其提供了針對混合式雲端工作負載的可見性和控制能力、可降低威脅曝露度的主動防禦措施、還有智慧型偵測可幫助您跟上網路攻擊快速發展的腳步。


## <a name="free-option-vs-azure-defender-enabled"></a>免費選項及已啟用 Azure Defender

資訊安全中心提供兩個模式：

- **關閉 Azure Defender** (免費) - 當您第一次造訪 Azure 入口網站中的 [Azure 資訊安全中心] 儀表板，或透過 API 以程式設計方式啟用此功能時，您所有的 Azure 訂用帳戶都會免費啟用不包含 Azure Defender 的資訊安全中心。 此免費模式會提供安全性原則、持續的安全性評估，以及可操作的安全性建議，以幫助您保護 Azure 資源。

- **開啟 Azure Defender** - 啟用 Azure Defender 會將免費模式的功能擴展到在私人雲端和其他公用雲端中執行的工作負載，為您的混合式雲端工作負載提供統一的安全性管理和威脅防護。 Azure Defender 的一些主要功能：

    - **混合式安全性** – 取得您所有內部部署和雲端工作負載中安全性的統一檢視。 套用安全性原則，並持續評估您混合式雲端工作負載的安全性，以確保符合安全性標準。 從多種來源 (包括防火牆和其他合作夥伴解決方案) 收集、搜尋及分析安全性資料。
    - **威脅防護警示** - 進階的行為分析和 Microsoft Intelligent Security Graph 可勝過不斷演進的網路攻擊。 有效率的調控內建行為分析和機器學習服務，以發現攻擊和零時差惡意探索。 監視網路、機器和雲端服務中是否有傳入攻擊和侵入後活動。 使用互動式工具和內容相關威脅情報來簡化調查。
    - **虛擬機器和容器登錄的弱點掃描** - 輕鬆地將掃描器部署到您所有的虛擬機器，以提供業界最先進的弱點管理解決方案。 直接在資訊安全中心內檢視、調查和補救結果。 
    - **存取與應用程式控制** - 套用依特定工作負載調整並由機器學習服務提供支援的建議，以建立允許與拒絕清單來封鎖惡意程式碼和其他不想要的應用程式。 使用 Just-In-Time 來控制針對 Azure VM 上管理連接埠的存取，以減少您網路的受攻擊面。 這會大幅減少暴力密碼破解和其他網路攻擊的風險。
    - **容器安全性功能** - 受益於您容器化環境的弱點管理和即時威脅防護。 啟用**適用於容器登錄的 Azure Defender** 時，最多可能需要 12 小時才能啟用所有功能。 我們會以推送至已連線登錄的唯一容器映像數目來計費。 在映像經過一次掃描之後，您就不需要再向此支付費用，除非進行修改並再推送一次。 

## <a name="try-azure-defender-free-for-30-days"></a>免費試用 Azure Defender 30 天

Azure Defender 在前 30 天免費。 在 30 天結束時，如果您選擇繼續使用服務，我們會自動開始針對使用量計費。

## <a name="enable-azure-defender"></a>啟用 Azure Defender

您可以使用 Azure Defender 來保護整個 Azure 訂用帳戶，而且訂用帳戶內的所有資源都會繼承保護。

若要啟用 Azure Defender：

1. 在資訊安全中心的主要功能表中，選取 [定價和設定]。
1. 選取您要升級的訂用帳戶。
1. 選取 [開啟 Azure Defender] 以進行升級。
1. 選取 [儲存]。

以下是範例訂用帳戶的定價頁面。 您會發現，Azure Defender 中的每個方案都是分開定價，而且可以個別設定為開啟或關閉。

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="入口網站中的資訊安全中心定價頁面":::

> [!NOTE]
> 若要啟用所有資訊安全中心功能 (包括威脅防護功能)，您必須在包含適用工作負載的訂用帳戶上啟用 Azure Defender。 在工作區層級上啟用並不會啟用 Just-In-Time VM 存取、自適性應用程式控制，以及 Azure 資源的網路偵測。 
>
> 您可以在訂用帳戶層級或資源層級啟用**適用於儲存體帳戶的 Azure Defender**。
> 您可以在訂用帳戶層級或資源層級啟用**適用於 SQL 的 Azure Defender**。
> 您只能在資源層級為**適用於 MariaDB/MySQL/PostgreSQL 的 Azure 資料庫**啟用威脅防護。


## <a name="next-steps"></a>後續步驟
在本文中，已向您介紹資訊安全中心的價格。 如需相關內容，請參閱：

- [如何最佳化您的 Azure 工作負載成本](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [以所選貨幣和區域為依據的定價詳細資料](https://azure.microsoft.com/pricing/details/security-center/)
- 您可能會想要管理您的成本，並透過將解決方案限制在一組特定的代理程式，來限制針對該解決方案所收集的資料量。 [解決方案目標](../operations-management-suite/operations-management-suite-solution-targeting.md)可讓您將某個範圍套用至解決方案，並將工作區中的電腦子集設定為目標。 如果您使用解決方案目標鎖定，資訊安全中心會將工作區列為沒有解決方案。