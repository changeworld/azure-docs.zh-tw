---
title: Azure 春季雲端異地災難修復 |Microsoft Docs
description: 瞭解如何保護您的春天雲端應用程式免于區域中斷
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279145"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure 春季雲端嚴重損壞修復

本文說明一些您可以用來保護 Azure 春季雲端應用程式不會發生停機的策略。  任何區域或資料中心可能會遇到因區域性災難而造成的停機時間，但仔細規劃可以減輕對客戶的影響。

## <a name="plan-your-application-deployment"></a>規劃您的應用程式部署

Azure 春季雲端應用程式會在特定區域中執行。  Azure 能在世界各地多個地理位置運作。 Azure 地理位置是包含至少一個 Azure 區域的已定義世界區域。 Azure 區域是地理位置內的一個區域，其中包含一或多個資料中心。  每個 Azure 區域都會與相同地理位置內的另一個區域配對，以共同形成區域配對。 Azure 會跨區域配對序列化平臺更新（預定的維護），以確保一次只會更新每組中的一個區域。 如果有中斷的情況影響到多個區域，每個配對中至少有一個區域會優先進行復原。

若要確保高可用性並防止災難，必須將您的春天雲端應用程式部署到多個區域。  Azure 提供[配對區域](../best-practices-availability-paired-regions.md)的清單，讓您能夠規劃雲端部署與區域配對。  我們建議您在設計微服務架構時考慮三個重要因素：區域可用性、Azure 配對區域和服務可用性。

*  區域可用性：選擇靠近使用者的地理區域，以將網路延遲和傳輸時間降至最低。
*  Azure 配對區域：選擇您所選地理區域內的配對區域，以確保協調的平臺更新和優先順序的復原工作（如有需要）。
*  服務可用性：決定配對的區域是否應執行熱/經常性存取、經常性存取/暖性或熱/冷。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理員來路由傳送流量

[Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)提供以 DNS 為基礎的流量負載平衡，並可將網路流量分散到多個區域。  使用 Azure 流量管理員將客戶導向至最接近的 Azure 春季雲端服務實例。  為了達到最佳效能和冗余，請在將所有應用程式流量傳送至您的 Azure 春季雲端服務之前，將其導向至 Azure 流量管理員。

如果您在多個區域中有 Azure 春季雲端應用程式，請使用 Azure 流量管理員控制每個區域中您應用程式的流量流程。  使用服務 IP 定義每個服務的 Azure 流量管理員端點。 客戶應連接到指向 Azure 春季雲端服務的 Azure 流量管理員 DNS 名稱。  Azure 流量管理員負載平衡已定義端點間的流量。  如果發生嚴重損壞的資料中心，Azure 流量管理員會將來自該區域的流量導向其配對，以確保服務持續性。