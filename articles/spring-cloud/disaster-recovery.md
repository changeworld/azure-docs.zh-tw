---
title: Azure 春雲地質災害恢復 |微軟文檔
description: 瞭解如何保護您的春雲應用程式免受區域中斷
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279145"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure 春雲災害復原

本文介紹了一些可用於保護 Azure 春雲應用程式免受停機的策略。  任何區域或資料中心都可能會遇到區域災難造成的停機，但仔細規劃可以減輕對客戶的影響。

## <a name="plan-your-application-deployment"></a>規劃應用程式部署

Azure 春雲應用程式在特定區域中運行。  Azure 能在世界各地多個地理位置運作。 Azure 地理位置是包含至少一個 Azure 區域的已定義世界區域。 Azure 區域是地理位置中的一個區域，包含一個或多個資料中心。  每個 Azure 區域都會與相同地理位置內的另一個區域配對，以共同形成區域配對。 Azure 跨區域對序列化平臺更新（計畫維護），確保每次僅更新每個對中的一個區域。 如果有中斷的情況影響到多個區域，每個配對中至少有一個區域會優先進行復原。

確保高可用性和抵禦災難需要將 Spring Cloud 應用程式部署到多個區域。  Azure 提供[配對區域](../best-practices-availability-paired-regions.md)的清單，以便您可以將春雲部署規劃為區域對。  我們建議您在設計微服務體系結構時考慮三個關鍵因素：區域可用性、Azure 配對區域和服務可用性。

*  區域可用性：選擇靠近使用者的地理區域，以最大限度地減少網路延遲和傳輸時間。
*  Azure 配對區域：選擇所選地理區域內的配對區域，以確保協調平臺更新，並在需要時確定恢復工作的優先順序。
*  服務可用性：決定配對區域是熱/熱、熱/熱還是熱/冷。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理員來路由傳送流量

[Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)提供基於 DNS 的流量負載平衡，並可以跨多個區域分發網路流量。  使用 Azure 流量管理器將客戶定向到離他們最近的 Azure Spring 雲服務實例。  為了獲得最佳性能和冗余，請通過 Azure 流量管理器引導所有應用程式流量，然後再將其發送到 Azure Spring 雲服務。

如果在多個區域中具有 Azure 春雲應用程式，請使用 Azure 流量管理器控制每個區域中應用程式的流量。  使用服務 IP 為每個服務定義 Azure 流量管理器終結點。 客戶應連接到指向 Azure Spring 雲服務的 Azure 流量管理器 DNS 名稱。  Azure 流量管理器負載平衡跨已定義終結點的流量。  如果災難襲擊資料中心，Azure 流量管理器將指示來自該區域的流量到其對，從而確保服務的連續性。