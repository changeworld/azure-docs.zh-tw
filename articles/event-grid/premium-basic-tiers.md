---
title: Azure 事件方格進階層和基本層
description: 本文說明 Azure 事件方格進階層和基本層之間的差異，以及每一層的使用時機
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300332"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure 事件方格進階層和基本層
Azure 事件方格有兩個層級：**進階**和**基本**。 基本層會使用取用定價或隨用隨付定價。 其提供您使用事件方格時所需的所有基本發行/訂閱工具，以便您取得事件驅動的程式設計模型。 進階層會進一步利用以企業為目標的安全性功能。 進階層是早期**預覽版**，其中許多功能仍在開發中。

## <a name="overview"></a>概觀
事件方格中的所有自訂主題和網域都屬於基本層或進階層。 從 `2020-04-01-preview` API 版本開始，您可以在建立主題或網域時選擇所需的階層。 預設值為基本層。 使用舊版 API 建立的主題和網域會預設為基本層。 若要變更主題和網域的定價層，請參閱[如何更新主題和網域的層級](update-tier.md)。

## <a name="capabilities-and-features"></a>功能

下表說明兩層級間的差異：

|       &nbsp;                                           | 基本           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| 用於輸入的 IP 防火牆規則                          | 預覽  | 預覽 |
| 用於輸出的服務標記                                | 預覽  | 預覽 |
| 輸入上的私人端點 VNet 整合          | 無法使用   | 預覽 |

## <a name="availability"></a>可用性
在初始預覽期間，下列區域中會提供具有私人端點整合的進階層主題和網域：

- 美國東部
- 美國西部 2
- 美國中南部

## <a name="pricing-and-quotas"></a>定價和配額
如需使用基本層的定價詳細資料，請參閱[事件方格定價](https://azure.microsoft.com/pricing/details/event-grid/)。 進階層定價尚未宣佈，在提供定價之前，您可以免費使用。

進階層和基本層上的現有主題和網域計數配額及輸送量，會提供到宣佈進階層定價為止。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- 若要從基本層升級至進階層，請參閱[更新定價層](update-tier.md)一文。 
- 您可以設定事件方格資源的 IP 防火牆，以限制只能從一組選取的 IP 位址或 IP 位址範圍存取公用網際網路。 如需逐步指示，請參閱 [設定防火牆](configure-firewall.md)。
- 您可以設定私人端點，以限制只允許來自所選虛擬網路的存取。 如需逐步指示，請參閱[設定私人端點](configure-private-endpoints.md)。