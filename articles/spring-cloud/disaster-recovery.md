---
title: Azure Spring Cloud 異地災害復原 | Microsoft Docs
description: 了解如何保護您的 Spring Cloud 應用程式，以避免發生區域中斷
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 81ca6b2c365b0dd8a249a337f51d78516cb2cc61
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657186"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure Spring Cloud 災害復原

本文說明一些您可以用來保護 Azure Spring Cloud 應用程式，以避免發生停機的策略。  任何區域或資料中心都可能會遭遇因區域性災害而造成的停機狀況，但謹慎規劃可以減緩對客戶的影響。

## <a name="plan-your-application-deployment"></a>規劃您的應用程式部署

Azure Spring Cloud 應用程式會在特定區域中執行。  Azure 能在世界各地多個地理位置運作。 Azure 地理位置是包含至少一個 Azure 區域的已定義世界區域。 Azure 區域是地理位置內的區域，包含一或多個資料中心。  每個 Azure 區域都會與相同地理位置內的另一個區域配對，以共同形成區域配對。 Azure 會跨區域配對序列化平台更新 (計劃性維護)，確保每個配對中一次只更新一個區域。 如果有中斷的情況影響到多個區域，每個配對中至少有一個區域會優先進行復原。

若要確保高可用性並防止發生災害，必須將您的 Spring Cloud 應用程式部署到多個區域。  Azure 提供[配對區域](../best-practices-availability-paired-regions.md)的清單，以便您規劃將 Spring Cloud 部署到區域配對。  我們建議您在設計微服務架構時，考慮三個重要因素：區域可用性、Azure 配對區域和服務可用性。

*  區域可用性：選擇靠近使用者的地理區域，將網路延遲和傳輸時間降至最低。
*  Azure 配對區域：在您選擇的地理區域內選擇配對的區域，以確保協調的平台更新和排定優先順序的復原工作 (如有需要) 順利進行。
*  服務可用性： 決定您的配對區域是否應執行熱/熱、熱/暖，或熱/冷。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理員來路由傳送流量

[Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)是以 DNS 為基礎的流量負載平衡器，可以將網路流量分散到多個區域。  使用 Azure 流量管理員，將客戶導向最靠近他們的 Azure Spring Cloud 服務執行個體。  若要獲得最佳效能和備援能力，請先透過 Azure 流量管理員引導所有應用程式流量，然後再將流量傳送至您的 Azure Spring Cloud 服務。

如果您在多個區域中有 Azure Spring Cloud 應用程式，請使用 Azure 流量管理員來控制每個區域中的應用程式流量流程。  使用服務 IP 為每個服務定義 Azure 流量管理員端點。 客戶應連線至指向 Azure Spring Cloud 的 Azure 流量管理員 DNS 名稱。  Azure 流量管理員負載會平衡已定義端點間的流量。  如果資料中心遭受災害攻擊，Azure 流量管理員會將來自該區域的流量導向其配對，以確保服務持續性。

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>為 Azure Spring Cloud 建立 Azure 流量管理員

1. 在兩個不同的區域中建立 Azure Spring Cloud。
您需要在兩個不同的區域中 (美國東部和西歐) 部署兩個 Azure Spring Cloud 的服務執行個體。 使用 Azure 入口網站來啟動現有的 Azure Spring Cloud 應用程式，以建立兩個服務執行個體。 每個執行個體都會做為流量的主要和容錯移轉端點。 

**兩個服務執行個體資訊：**

| 服務名稱 | Location | Application |
|--|--|--|
| service-sample-a | 美國東部 | gateway / auth-service / account-service |
| service-sample-b | 西歐 | gateway / auth-service / account-service |

2. 遵循[自訂網域文件](spring-cloud-tutorial-custom-domain.md)設定服務的自訂網域，以針對這兩個現有的服務執行個體設定自訂網域。 成功設定之後，兩個服務執行個體皆會繫結至自訂網域：bcdr-test.contoso.com

3. 建立流量管理員與兩個端點：[使用 Azure 入口網站建立流量管理員](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile)。

以下是流量管理員設定檔：
* 流量管理員 DNS 名稱：`http://asc-bcdr.trafficmanager.net`
* 端點設定檔： 

| 設定檔 | 類型 | 目標 | 優先順序 | 自訂標頭設定 |
|--|--|--|--|--|
| 端點 A 設定檔 | 外部端點 | service-sample-a.asc-test.net | 1 | host: bcdr-test.contoso.com |
| 端點 B 設定檔 | 外部端點 | service-sample-b.asc-test.net | 2 | host: bcdr-test.contoso.com |

4. 在 DNS 區域中建立 CNAME 記錄：bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net。 

5. 現在，環境已設定完成。 客戶應該可以透過 bcdr-test.contoso.com 存取應用程式