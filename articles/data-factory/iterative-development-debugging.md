---
title: Azure 資料工廠的反覆發開發並除錯
description: 了解如何在 Azure 入口網站中反覆地開發和偵錯 Data Factory 管線。
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 49e406f0730189439bb1c2529b23d8ad6d760a2f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411519"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>透過 Azure Data Factory 的反覆式開發與偵錯
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 可讓您反覆地開發和偵錯 Data Factory 管線。

如需此功能的簡介與示範，請觀看下列 8 分鐘長的影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>反覆式偵錯功能
使用管線畫布中的 [偵錯]**** 功能來建立管線和執行測試回合，而不需撰寫任何一行程式碼。

![管線畫布上的偵錯功能](media/iterative-development-debugging/iterative-development-image1.png)

在管線畫布的 [輸出]**** 視窗中檢視測試回合的結果。

![管線畫布的輸出視窗](media/iterative-development-debugging/iterative-development-image2.png)

測試回合成功之後，將更多活動新增至您的管線並繼續以反覆方式進行偵錯。 您也可以 [取消]**** 正在進行的測試回合。

![取消測試回合](media/iterative-development-debugging/iterative-development-image3.png)

當您執行測試回合時，您不必在選取 [偵錯]**** 前，先將變更發佈至資料處理站。 如果您想要在更新資料處理站工作流程之前，先確保變更如預期般運作，此功能會很有幫助。

> [!IMPORTANT]
> 選取 [偵錯]**** 實際上會執行管線。 因此，如果管線包含複製活動，則測試回合將資料從來源複製到目的地。 如此一來，我們建議您在偵錯時，於複製活動或其他活動中使用測試資料夾。 完成管線偵錯之後，請切換到您要在正常作業中使用的實際資料夾。

## <a name="visualizing-debug-runs"></a>將偵錯回合視覺化

您可以將資料處理站的所有進行中偵錯回合在一個地方進行視覺化。 請選取頁面右上角的 [檢視偵錯回合]****。 如果您的主要管線開始進行子管線的偵錯回合，而您想要有一個可查看所有作用中偵錯回合的單一檢視，此功能便相當有用。

![選取 [檢視作用中偵錯回合] 圖示](media/iterative-development-debugging/view-debug-runs-image1.png)

![作用中偵錯回合的範例清單](media/iterative-development-debugging/view-debug-runs-image2.png)

如果您有活動的數據流調試會話,這些會話將顯示在活動調試視窗的底部。 您可以選擇活動資料流程作業並停止相應的群集。

![活動資料流程除錯執行的範例清單](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>監視偵錯回合

使用**除錯**功能啟動的測試執行在 **「監視器」** 選項卡的清單中不可用。您只能在 **「監視器」** 選項卡中看到使用 **「立即觸發**」、**計畫**或**翻滾視窗**觸發器觸發的運行。您可以在管線圖布的 **「輸出」** 視窗中看到使用**除錯**功能啟動的最後一次測試執行。

## <a name="setting-breakpoints-for-debugging"></a>設定偵錯的中斷點

Data Factory 也可讓您進行偵錯，直到您到達管線畫布上的特定活動為止。 只要將中斷點放在您要測試時的活動，然後選取 [偵錯]****。 Data Factory 可確保測試回合只會進行到管線畫布上的中斷點活動。 如果您不想測試整個管線，而只想測試管線內的部分活動，這項 [偵錯直到]** 功能很實用。

![管線畫布上的中斷點](media/iterative-development-debugging/iterative-development-image4.png)

若要設定中斷點，請選取管線畫布上的元素。 [偵錯直到]** 選項會在元素的右上角顯示為空心的紅色圓圈。

![對選取的元素設定中斷點之前](media/iterative-development-debugging/iterative-development-image5.png)

在您選取 [偵錯直到]** 選項後，它會變更為實心的紅色圓圈，以指出中斷點已啟用。

![對選取的元素設定中斷點之後](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>後續步驟
[Azure Data Factory 中的持續整合和部署](continuous-integration-deployment.md)
