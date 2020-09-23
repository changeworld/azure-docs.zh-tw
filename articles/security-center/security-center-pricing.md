---
title: Azure 資訊安全中心的定價
description: Azure 資訊安全中心會以兩種模式提供，且不含 Azure Defender。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2020
ms.author: memildin
ms.openlocfilehash: 6d2127af2c2c9e04141551dae72f0177f495b165
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904775"
---
# <a name="pricing-of-azure-security-center"></a>Azure 資訊安全中心的定價
Azure 資訊安全中心提供統一的安全性管理和進階的威脅防護，保護 Azure、內部部署和其他雲端中執行的工作負載。 其提供了針對混合式雲端工作負載的可見性和控制能力、可降低威脅曝露度的主動防禦措施、還有智慧型偵測可幫助您跟上網路攻擊快速發展的腳步。


## <a name="free-option-vs-azure-defender-enabled"></a>免費選項與 Azure Defender 已啟用

安全中心提供兩種模式：

- 當您第一次造訪 Azure 入口網站中的 Azure 資訊安全中心儀表板，或透過 API 以程式設計方式啟用時，您的所有 Azure 訂用帳戶都不會在您的 Azure 訂用帳戶上免費啟用**Azure defender** (免費) -資訊安全中心。 使用此免費模式可提供安全性原則、持續的安全性評估，以及可操作的安全性建議，以協助您保護您的 Azure 資源。

- 啟用 azure defender**的 azure defender**可將免費模式的功能延伸至在私用和其他公用雲端中執行的工作負載，為混合式雲端工作負載提供統一的安全性管理和威脅防護。 Azure Defender 的一些主要功能：

    - **混合式安全性** – 取得您所有內部部署和雲端工作負載中安全性的統一檢視。 套用安全性原則，並持續評估您混合式雲端工作負載的安全性，以確保符合安全性標準。 從多個來源（包括防火牆和其他合作夥伴解決方案）收集、搜尋及分析安全性資料。
    - **威脅防護警示** -Advanced 行為分析和 Microsoft Intelligent Security Graph 可為不斷演進的網路攻擊提供邊緣。 有效率的調控內建行為分析和機器學習服務，以發現攻擊和零時差惡意探索。 監視網路、機器和雲端服務中是否有傳入攻擊和侵入後活動。 使用互動式工具和內容相關威脅情報來簡化調查。
    - **虛擬機器和容器登錄的弱點掃描** -輕鬆地將掃描器部署至所有虛擬機器，以提供業界最先進的弱點管理解決方案。 直接在安全性中心內查看、調查和補救結果。 
    - **存取和應用** 程式控制-藉由套用機器學習服務的建議，並調整為您的特定工作負載來建立允許和拒絕清單，以封鎖惡意程式碼和其他不必要的應用程式。 使用即時控制存取 Azure Vm 上的管理埠，來減少網路攻擊面。 這可大幅減少暴力密碼破解攻擊和其他網路攻擊的風險。
    - **容器安全性功能** -在容器化環境中受益于弱點管理和即時威脅防護。 啟用容器登錄的 **Azure Defender**時，在啟用所有功能之前，可能需要12hrs。 費用是根據推送至連線登錄的唯一容器映射數目。 映射掃描一次之後，除非經過修改並推送一次，否則您不會再向您收費。 

## <a name="try-azure-defender-free-for-30-days"></a>免費試用 Azure Defender 30 天

Azure Defender 的前30天免費。 30天結束時，如果您選擇繼續使用服務，我們會自動開始收取使用量的費用。

## <a name="enable-azure-defender"></a>啟用 Azure Defender

您可以使用 Azure Defender 保護整個 Azure 訂用帳戶，而且訂用帳戶內的所有資源都會繼承保護。

若要啟用 Azure Defender：

1. 從 [安全性中心] 的主功能表中，選取 [ **定價 & 設定**]。
1. 選取您要升級的訂用帳戶。
1. 選取 [要升級 **的 Azure Defender** ]。
1. 選取 [儲存]。

以下是範例訂用帳戶的定價頁面。 您會發現，Azure Defender 中的每個方案都是分開定價，而且可以個別設定為 on 或 off。

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="入口網站中的 [Security Center 定價] 頁面":::

> [!NOTE]
> 若要啟用所有資訊安全中心功能，包括威脅防護功能，您必須在包含適用工作負載的訂用帳戶上啟用 Azure Defender。 在工作區層級啟用它，並不會啟用即時 VM 存取、適應性應用程式控制，以及適用于 Azure 資源的網路偵測。 
>
> 您可以在訂用帳戶層級或資源層級啟用 **儲存體帳戶的 Azure Defender** 。
> 您可以在訂用帳戶層級或資源層級啟用 **Azure Defender FOR SQL** 。
> 您只能在資源層級為**適用於 MariaDB/MySQL/PostgreSQL 的 Azure 資料庫**啟用威脅防護。


## <a name="next-steps"></a>下一步
在本文中，已向您介紹資訊安全中心的價格。 如需相關內容，請參閱：

- [如何優化您的 Azure 工作負載成本](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [定價詳細資料，以您選擇的貨幣，以及根據您的區域](https://azure.microsoft.com/pricing/details/security-center/)
- 您可能會想要管理您的成本，並透過將解決方案限制在一組特定的代理程式，來限制針對該解決方案所收集的資料量。 [解決方案目標](../operations-management-suite/operations-management-suite-solution-targeting.md)可讓您將某個範圍套用至解決方案，並將工作區中的電腦子集設定為目標。 如果您使用解決方案目標鎖定，資訊安全中心會將工作區列為沒有解決方案。