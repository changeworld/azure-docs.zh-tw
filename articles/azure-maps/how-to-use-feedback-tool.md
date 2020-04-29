---
title: 提供 Azure 地圖服務的資料意見反應 |Microsoft Azure 對應
description: 使用 Microsoft Azure Maps 意見反應工具來提供資料意見反應。
author: philmea
ms.author: philmea
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 92469370f1ea64f5ee1bc2a84e47cc8b294b5375
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335351"
---
# <a name="provide-data-feedback-to-azure-maps"></a>提供資料意見反應給 Azure 地圖服務

從2018年5月起，已可使用 Azure 地圖服務。 Azure 地圖服務已提供最新的地圖資料、便於使用的 REST Api 和強大的 Sdk，以支援企業客戶使用不同種類的商務使用案例。 真實世界每秒都會變更，而我們很重要的是，為我們的客戶提供實際的數位標記法。 打算開啟或關閉設施的客戶，需要我們的地圖服務才能立即更新。 因此，他們可以有效率地規劃在適當設施的傳遞、維護或客戶服務。 我們已建立 Azure 地圖服務資料意見反應網站，讓我們的客戶能夠提供直接的資料意見反應。 客戶的資料意見反應會直接傳送給我們的資料提供者及其地圖編輯器。 他們可以快速評估並納入我們的對應產品的意見反應。  

[Azure 地圖服務資料意見反應網站](https://feedback.azuremaps.com)提供簡單的方式，讓我們的客戶提供地圖資料的意見反應，特別是在感關注的業務點和住家住址。 本文會引導您瞭解如何使用 Azure 地圖服務意見反應網站來提供不同類型的意見反應。

## <a name="add-a-business-place-or-a-residential-address"></a>新增公司場所或住家住址 

您可能想要提供有關遺失點或住家住址的意見反應。 有兩種方式可以這麼做。 開啟 Azure 地圖資料意見反應網站，搜尋缺少位置的座標，然後按一下 [新增位置]

  ![搜尋缺少的位置](./media/how-to-use-feedback-tool/search-poi.png)

或者，您可以與地圖互動。 按一下位置以在座標處放置釘選，然後按一下 [新增位置]。

  ![新增 pin](./media/how-to-use-feedback-tool/add-poi.png)

按一下時，系統會將您導向至表單，以提供位置的對應詳細資料。

  ![新增位置](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>修正公司場所或住家住址 

意見反應網站也可讓您搜尋並找出公司地點或位址。 如果位址或 pin 位置不正確，您可以提供意見反應來加以修正。 若要提供意見反應以修正位址，請使用搜尋列來搜尋營業地點或住家住址。 從 [結果] 清單中，按一下您想要的位置。 按一下 [修正此位置]。

  ![要修正的搜尋位置](./media/how-to-use-feedback-tool/fix-place.png)

若要提供意見反應以修正位址，請填寫「修正位置」表單，然後按一下 [提交] 按鈕。

  ![修正表單](./media/how-to-use-feedback-tool/fix-form.png)

如果位置的 pin 位置錯誤，請核取 [修正位置] 表單上指出「pin 位置不正確」的核取方塊。 將 pin 移至正確的位置，然後按一下 [提交] 按鈕。

  ![移動釘選位置](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>新增意見 

除了可讓您搜尋位置之外，意見反應工具也可讓您新增自由格式文字批註，以取得與位置相關的詳細資料。 若要新增批註，請搜尋位置，或按一下位置。 按一下 [新增批註]、撰寫批註，然後按一下 [提交]。

  ![加入註解](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>追蹤狀態 

您也可以選取 [我想要追蹤狀態] 方塊，並在提出要求時提供您的電子郵件，以追蹤要求的狀態。 您會在電子郵件中收到追蹤連結，以提供要求的最新狀態。 

  ![意見反應狀態](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>後續步驟

若要張貼與 Azure 地圖服務相關的任何技術問題，請造訪：

* [Azure 地圖服務 Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure 地圖服務意見反應論壇](https://feedback.azure.com/forums/909172-azure-maps)
