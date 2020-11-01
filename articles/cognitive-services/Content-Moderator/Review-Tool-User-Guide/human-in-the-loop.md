---
title: 瞭解審核工具概念-內容仲裁
titleSuffix: Azure Cognitive Services
description: 深入瞭解內容仲裁審核工具，這是一個可協調 AI 和人工審核審核工作的網站。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146604"
---
# <a name="content-moderator-review-tool"></a>內容仲裁審核工具

Azure 內容仲裁提供的服務可結合機器學習內容仲裁與人工審核。 [審核工具](https://contentmoderator.cognitive.microsoft.com)網站是方便使用的前端，可讓您更詳細地存取這些服務。

## <a name="what-it-does"></a>作用

[審核工具](https://contentmoderator.cognitive.microsoft.com)搭配電腦輔助審核 api 使用時，可讓您在內容仲裁流程中完成下列工作：

- 您可以使用一組工具，以多種格式來仲裁內容 (文字、影像和影片) 。
- 在仲裁 API 結果開始時，自動建立人工 [審核](../review-api.md#reviews) 。
- 將內容評論指派或呈報給多個審核小組（依內容類別別或經驗等級排列）。
- 使用預設或自訂邏輯篩選 ([工作流程](../review-api.md#workflows)) 來排序和追蹤內容，而不需要撰寫任何程式碼。
- 除了內容仲裁 Api 之外，您還可以使用 [連接器](./configure.md#connectors) 來處理 Microsoft PhotoDNA、文字分析和臉部服務的內容。
- 取得您內容審核程序的關鍵效能計量。

## <a name="review-tool-dashboard"></a>審核工具儀表板

在 [ **儀表板** ] 索引標籤中，您可以看到在工具中完成的內容審核的重要度量。 查看影像、文字和影片內容的總計、完整和暫止的評論數目。 

[ **暫** 止的評論] 資料表會顯示有擱置或已完成評論的使用者和子小組明細，以及剩餘的 SLA 時間。 您可以選取資料表中的專案，以移至其評論。 資料表上方的 [搜尋] 方塊可讓您依小組名稱篩選結果，而 **篩選** 圖示可讓您依其他計量進行篩選。

切換至 [ **已完成的評論** ] 索引標籤會顯示使用者和子小組處理或完成的專案總數。 您可以將此資料與暫止的評論一樣進行篩選。

按一下儀表板右上角的文字，會顯示每日個人計量，以報告每個內容類型已完成的評論數目。

> [!div class="mx-imgBorder"]
> ![瀏覽器中的審核工具儀表板](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>審核工具認證

當您使用 [審核工具](https://contentmoderator.cognitive.microsoft.com)註冊時，系統會提示您為您的帳戶選取 Azure 區域。 這是因為 [審核工具](https://contentmoderator.cognitive.microsoft.com) 會產生 Azure 內容仲裁服務的免費試用金鑰。 您將需要此金鑰，才能從 REST 呼叫或用戶端 SDK 存取任何服務。 您可以藉由選取 [  。

> [!div class="mx-imgBorder"]
> ![Content Moderator 認證](images/settings-6-credentials.png)

## <a name="next-steps"></a>下一步

請參閱 [設定審核工具](./configure.md) ，以瞭解如何存取審核工具資源及變更設定。