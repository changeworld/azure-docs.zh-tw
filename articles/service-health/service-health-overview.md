---
title: 服務健康狀態概觀 | Microsoft Docs
description: 目前和未來的 Azure 服務問題和維修如何影響 Azure 應用程式的個人化資訊。
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a1c6a10ab48125c030c9a35864f829438404ad97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898446"
---
# <a name="service-health-overview"></a>服務運行狀況概述

服務健康狀態為您提供可自訂的儀表板，可追蹤您使用這些服務之所在區域中的 Azure 服務健康狀態。 在此儀表板中，您可以追蹤作用中的事件，例如，進行中的服務問題、近期預定的維修，或相關的健康狀態諮詢。 當事件變成非作用中時，它們會放置在健康狀態歷程記錄中最多 90 天。 最後，您可以使用服務健康狀態儀表板建立和管理服務健康狀態警示，當服務問題對您產生影響時，會主動予以通知。

## <a name="service-health-events"></a>服務健康情況事件

服務運行狀況跟蹤可能影響資源的四種類型的運行狀況事件：

1. **服務的問題** - 立即影響 Azure 服務的問題。 
2. **規劃的維護** - 未來可能影響服務可用性的後續維修。  
3. **健康情況摘要報告** - 需要注意的 Azure 服務變更。 範例包括取代 Azure 功能，或超過使用量配額。
4. **安全通報（預覽）** - 可能影響 Azure 服務可用性的安全相關通知。

> [!NOTE]
> 要查看服務運行狀況事件，必須[授予訂閱上的讀取器角色](../role-based-access-control/role-assignments-portal.md)。

## <a name="get-started-with-service-health"></a>開始使用服務健康情況

若要啟動服務健康情況儀表板，請選取入口網站儀表板上的 [服務健康情況] 圖格。 如果您先前已移除圖格，或您正在使用自訂的儀表板，搜尋「更多服務」(儀表板的左下方) 中的服務健康情況服務。

![開始使用服務健康情況](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>查看影響服務的目前問題

**服務問題**檢視會顯示 Azure 服務中任何正在發生並影響資源的問題。 您可以了解問題開始發生的時間，以及受影響的服務和區域。 您也可以參閱最新的更新，以了解 Azure 如何解決問題。 

![管理服務問題](./media/service-health-overview/azure-service-health-overview-2.png)

選擇 [潛在影響]**** 索引標籤，查看將受問題影響而且您擁有的資源列出的特定清單。 您可以下載這些資源的 CSV 清單，以便與小組共用。

![管理服務問題 - 影響](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>取得連結和可下載的說明 

您可以取得問題的連結，以便在您的問題管理系統中使用。 您可以下載 PDF，有時還可以下載 CSV 檔，以便與無法訪問 Azure 門戶的使用者共用。   

![管理服務問題 - 問題管理](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>獲得 Microsoft 的支援

若解決問題後您的資源仍處於不正常的狀態，請連絡支援人員。  使用頁面右邊的支援連結。  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>將個人化健康情況地圖釘選在儀表板

篩選服務健康情況，以便顯示您的商務關鍵訂用帳戶、區域和資源類型。 儲存篩選條件，並將個人化健康情況世界地圖釘選在入口網站儀表板。 

![篩選個人化健康情況地圖](./media/service-health-overview/azure-service-health-overview-6a.png)

![釘選個人化健康情況地圖](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>設定服務健康狀態警示

服務健康狀態已整合 Azure 監視器，當您的業務關鍵資源受到影響時，就會透過電子郵件、簡訊和 Webhook 通知向您發出警示。 設定適當服務健康狀態事件的活動記錄警示。 使用動作群組，將該警示傳送給組織中的適當人員。 有關詳細資訊，請參閱[為服務運行狀況配置警報](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>後續步驟

設定警示，如此就能收到健康情況問題的通知。 有關詳細資訊，請參閱設置[Azure 服務運行狀況警報的最佳做法](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s)。 
