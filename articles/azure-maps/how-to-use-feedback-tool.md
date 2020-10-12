---
title: 提供資料意見反應給 Azure 地圖服務 |Microsoft Azure 對應
description: 使用 Microsoft Azure Maps 意見反應工具提供資料意見反應。
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/19/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 807c8908b70065d5d82229e7750b166bfabcf53b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335359"
---
# <a name="provide-data-feedback-to-azure-maps"></a>提供資料意見反應給 Azure 地圖服務

自2018年5月起，Azure 地圖服務已可使用。 Azure 地圖服務已提供全新的地圖資料、便於使用的 REST Api，以及功能強大的 Sdk，以支援具有不同類型之商務使用案例的企業客戶。 真實世界正在每秒變更，因此請務必為客戶提供實際的數位標記法。 打算開啟或關閉設施的客戶需要我們的地圖立即更新。 因此，他們可以在適當的設備上有效率地規劃傳遞、維護或客戶服務。 我們已建立 Azure 地圖服務資料意見反應網站，讓客戶能夠提供直接資料的意見反應。 客戶的資料意見反應會直接移至我們的資料提供者及其地圖編輯器。 他們可以在我們的地圖產品中快速評估並納入意見反應。  

[Azure 地圖服務資料意見反應網站](https://feedback.azuremaps.com) 提供簡單的方法，讓客戶能夠提供地圖資料意見反應，特別是在感興趣的商務點和住家住址。 本文將引導您瞭解如何使用 Azure 地圖服務意見反應網站來提供不同類型的意見反應。

## <a name="add-a-business-place-or-a-residential-address"></a>新增業務地點或住家住址 

您可能會想要提供關於遺漏的景點或住家住址的意見反應。 有兩種方式可以這麼做。 開啟 Azure 地圖資料意見反應網站、搜尋遺失位置的座標，然後按一下 [新增位置]

  ![搜尋遺失的位置](./media/how-to-use-feedback-tool/search-poi.png)

或者，您可以與地圖互動。 按一下要在座標處放置圖釘的位置，然後按一下 [新增位置]。

  ![新增 pin](./media/how-to-use-feedback-tool/add-poi.png)

按一下時，系統會將您導向至表單，以提供位置的對應詳細資料。

  ![新增位置](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>修正業務地點或住家住址 

意見反應網站也可讓您搜尋及找出營業地點或位址。 您可以提供意見反應來修正位址或 pin 位置（如果不正確）。 若要提供意見反應以修正位址，請使用搜尋列來搜尋營業地點或居住位址。 從結果清單中，按一下您感興趣的位置。 按一下 [修正此地點]。

  ![要修正的搜尋位置](./media/how-to-use-feedback-tool/fix-place.png)

若要提供意見反應以修正位址，請填寫「修正地點」表單，然後按一下 [提交] 按鈕。

  ![修正表單](./media/how-to-use-feedback-tool/fix-form.png)

如果位置的 pin 位置錯誤，請核取「修正地點」表單上的核取方塊，指出「pin 位置不正確」。 將 pin 移至正確的位置，然後按一下 [提交] 按鈕。

  ![移動釘選位置](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>新增意見 

除了讓您搜尋某個位置之外，意見反應工具也可讓您新增自由格式文字批註，以取得與該位置相關的詳細資料。 若要加入批註，請搜尋位置或按一下位置。 按一下 [新增批註]、撰寫批註，然後按一下 [提交]。

  ![加入註解](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>追蹤狀態 

您也可以選取 [我想要追蹤狀態] 方塊，並在提出要求時提供您的電子郵件，以追蹤要求的狀態。 您將會在電子郵件中收到追蹤連結，以提供您要求的最新狀態。 

  ![意見反應狀態](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>後續步驟

若要張貼與 Azure 地圖服務相關的任何技術問題，請造訪：

* [Microsoft 問 & A](https://docs.microsoft.com/answers/topics/azure-maps.html)
