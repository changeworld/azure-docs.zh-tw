---
title: Azure 春季雲端異地災難修復 |Microsoft Docs
description: 瞭解如何保護您的春天雲端應用程式免于區域中斷
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: e8f32f574a4ff7be0cc3cc7915b8203b53824c63
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792321"
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

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>建立 azure 春季雲端的 Azure 流量管理員

1. 在兩個不同的區域中建立 Azure 春季雲端。
您需要在兩個不同的區域（美國東部和西歐）中部署兩個 Azure 春季雲端的服務實例。 使用 Azure 入口網站來啟動現有的 Azure 春季雲端應用程式，以建立兩個服務實例。 每個服務都會作為流量的主要和故障後端端點。 

**兩個服務實例資訊：**

| 服務名稱 | Location | 應用程式 |
|--|--|--|
| 服務-範例-a | 美國東部 | 閘道/驗證-服務/帳戶-服務 |
| 服務-範例-b | 西歐 | 閘道/驗證-服務/帳戶-服務 |

2. 設定服務的自訂網域遵循[自訂網域檔](spring-cloud-tutorial-custom-domain.md)，為這兩個現有的服務實例設定自訂網域。 成功設定之後，這兩個服務實例將會系結至自訂網域： bcdr-test.contoso.com

3. 建立流量管理員和兩個端點：[使用 Azure 入口網站建立流量管理員設定檔](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile)。

以下是流量管理員設定檔：
* 流量管理員 DNS 名稱：http://asc-bcdr.trafficmanager.net
* 端點設定檔： 

| 設定檔 | 類型 | 目標 | 優先順序 | 自訂標頭設定 |
|--|--|--|--|--|
| 端點設定檔 | 外部端點 | service-sample-a.asc-test.net | 1 | 主機： bcdr-test.contoso.com |
| 端點 B 設定檔 | 外部端點 | service-sample-b.asc-test.net | 2 | 主機： bcdr-test.contoso.com |

4. 在 DNS 區域中建立 CNAME 記錄： bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net。 

5. 現在環境已完全設定完成。 客戶應該能夠透過下列方式存取應用程式： bcdr-test.contoso.com