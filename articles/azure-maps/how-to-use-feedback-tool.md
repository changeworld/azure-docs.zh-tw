---
title: 向 Azure 地圖提供資料回饋 |微軟 Azure 地圖
description: 使用 Microsoft Azure 地圖回饋工具提供資料回饋。
author: philmea
ms.author: philmea
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 92469370f1ea64f5ee1bc2a84e47cc8b294b5375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335351"
---
# <a name="provide-data-feedback-to-azure-maps"></a>向 Azure 地圖提供資料回饋

Azure 地圖自 2018 年 5 月起提供。 Azure 地圖一直提供新的地圖資料、便於使用的 REST API 和強大的 SDK，以便為企業提供不同類型的業務用例。 現實世界每時每一秒都在變化，我們為客戶提供真實的數位表示至關重要。 計畫開放或關閉設施的客戶需要我們的地圖及時更新。 因此，他們可以在正確的設施內高效地規劃交付、維護或客戶服務。 我們創建了 Azure 地圖資料回饋網站，使我們的客戶能夠提供直接的資料回饋。 客戶的資料回饋直接回饋給我們的資料供應商及其地圖編輯器。 他們可以快速評估和將回饋納入我們的地圖產品。  

[Azure 地圖資料回饋網站](https://feedback.azuremaps.com)為我們的客戶提供了一種簡單的方法來提供地圖資料回饋，尤其是有關業務興趣點和住家住址。 本文將指導您如何使用 Azure 地圖回饋網站提供不同類型的回饋。

## <a name="add-a-business-place-or-a-residential-address"></a>添加營業場所或住家住址 

您可能需要提供有關缺少興趣點或住家住址的回饋。 有兩種方式可以這麼做。 打開 Azure 地圖資料回饋網站，搜索缺失位置的座標，然後按一下"添加位置"

  ![搜索缺失位置](./media/how-to-use-feedback-tool/search-poi.png)

或者，您可以與地圖進行交互。 按一下該位置在座標處放置一個圖釘，然後按一下"添加位置"。

  ![添加引腳](./media/how-to-use-feedback-tool/add-poi.png)

按一下後，您將被引導到表單中，以提供地點的相應詳細資訊。

  ![添加位置](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>修復營業場所或住家住址 

回饋網站還允許您搜索和定位營業場所或位址。 如果位址或引腳位置不正確，則可以提供回饋來修復它們。 要提供修復位址的回饋，請使用搜索欄搜索營業地或住家住址。 從結果清單中按一下您感興趣的位置。 按一下"修復此位置"。

  ![要修復的搜索位置](./media/how-to-use-feedback-tool/fix-place.png)

要提供回饋以修復位址，請填寫"修復地點"表單，然後按一下"提交"按鈕。

  ![修復表單](./media/how-to-use-feedback-tool/fix-form.png)

如果位置的引腳位置錯誤，請檢查"修復位置"表單上的核取方塊，該核取方塊顯示"引腳位置不正確"。 將圖釘移到正確的位置，然後按一下"提交"按鈕。

  ![移動銷位置](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>新增意見 

除了允許您搜索位置外，回饋工具還允許您添加與位置相關的詳細資訊的免費表單文本注釋。 要添加注釋，請搜索該位置或按一下該位置。 按一下"添加注釋"，撰寫注釋，然後按一下"提交"。

  ![加入註解](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>追蹤狀態 

您還可以通過選中"我想跟蹤狀態"框並在發出請求時提供電子郵件來跟蹤您的請求的狀態。 您將在電子郵件中收到一個跟蹤連結，該連結提供您的請求的最新狀態。 

  ![回饋狀態](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>後續步驟

要發佈與 Azure 地圖相關的任何技術問題，請訪問：

* [Azure 映射堆疊溢位](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure 地圖回饋論壇](https://feedback.azure.com/forums/909172-azure-maps)
