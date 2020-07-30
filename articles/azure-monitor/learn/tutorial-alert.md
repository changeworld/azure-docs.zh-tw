---
title: 透過 Azure Application Insights 傳送警示 | Microsoft Docs
description: 使用 Azure Application Insights 傳送警示以回應應用程式錯誤的教學課程。
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 706f3913e25eca6240c186e45709faf6c77620bf
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324568"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>使用 Azure Application Insights 監視應用程式健康情況並傳送警示

Azure Application Insights 可讓您監視您的應用程式，並在應用程式無法使用、發生失敗或有效能問題時傳送警示給您。  本教學課程會引導您完成建立測試的程序，以持續檢查您應用程式的可用性。

您會了解如何：

> [!div class="checklist"]
> * 建立可用性測試，以持續檢查應用程式的回應
> * 發生問題時，傳送郵件給系統管理員

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

建立 [Application Insights 資源](./dotnetcore-quick-start.md#enable-application-insights)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-availability-test"></a>建立可用性測試

Application Insights 中的可用性測試，可讓您從世界各地的不同位置自動測試您的應用程式。   在本教學課程中，您會執行 URL 測試，以確定您的 Web 應用程式可以使用。  您也可以建立完整的檢測程序來測試詳細作業。 

1. 選取 [Application Insights]  ，然後選取您的訂用帳戶。  

2. 選取 [調查] 功能表下方的 [可用性]，然後按一下 [建立測試]。

    ![加入可用性測試](media/tutorial-alert/add-test-001.png)

3. 輸入測試的名稱，並保留其他預設值。  此選取項目會每隔 5 分鐘從 5 個不同的地理位置觸發對應用程式 URL 的要求。

4. 選取 [警示]  以開啟 [警示]  下拉式清單，您可在該處定義測試失敗時如何回應的詳細資料。 選擇 [近乎即時]  並將狀態設定為 [已啟用]  。

    輸入符合警示準則時要傳送到哪個電子郵件地址。  您也可以輸入符合警示準則時要呼叫的 Webhook 位址。

    ![建立測試](media/tutorial-alert/create-test-001.png)

5. 返回測試面板，選取省略符號並編輯警示，為您的近乎即時警示輸入組態。

    ![編輯警示](media/tutorial-alert/edit-alert-001.png)

6. 將失敗的位置設定為大於或等於 3。 建立[動作群組](../platform/action-groups.md)，以設定在違反警示閾值時收到通知的對象。

    ![儲存警示 UI](media/tutorial-alert/save-alert-001.png)

7. 設定好您的警示後，請按一下測試名稱以檢視來自每個位置的詳細資料。 您可以折線圖和散佈圖格式檢視測試，以視覺化方式呈現指定時間範圍內的成功/失敗。

    ![測試詳細資料](media/tutorial-alert/test-details-001.png)

8. 您可以按一下散佈圖上的任一點，向下鑽研至該測試的詳細資料。 這將會啟動端對端交易詳細資料檢視。 下列範例顯示失敗要求的詳細資料。

    ![測試結果](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>後續步驟

由於您已學會如何針對問題傳送警示，請前進到下一個教學課程，了解如何分析使用者和您應用程式的互動方式。

> [!div class="nextstepaction"]
> [了解使用者](./tutorial-users.md)

