---
title: 在 Excel 中取用 Web 服務
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio （傳統）可讓您輕鬆地直接從 Excel 呼叫 web 服務，而不需要撰寫任何程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 2e95c4bfbe7342e251e6d845fd4acfed6ff6109a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150097"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>從 Excel 使用 Azure Machine Learning Studio （傳統） Web 服務

 Azure Machine Learning Studio （傳統）可讓您輕鬆地直接從 Excel 呼叫 web 服務，而不需要撰寫任何程式碼。

如果您使用 Excel 2013 (或更新版本) 或 Excel Online，我們建議您使用 [Excel 增益集](excel-add-in-for-web-services.md)。



## <a name="steps"></a>步驟
發佈 Web 服務。 [教學課程3：部署信用風險模型](tutorial-part3-credit-risk-deploy.md)說明如何執行這項操作。 目前，只針對具有單一輸出 (也就是單一計分標籤) 的要求/回應服務，才支援 Excel 活頁簿功能。 

有了 Web 服務之後，請按一下 Studio 左側的 [Web 服務] 區段，然後選取要從 Excel 使用的 Web 服務。

**傳統 Web 服務**

1. Web 服務的 [儀表板] 索引標籤上有一列 [要求/回應] 服務。 如果此服務有單一的輸出，您應該會在該列中看到 [下載 Excel 活頁簿] 連結。

    ![使用 Studio （傳統） Web 服務入口網站下載 Excel 活頁簿](./media/consuming-from-excel/excellink.png)
2. 按一下 [下載 Excel 活頁簿]。

**新的 Web 服務**

1. 在 Azure Machine Learning Web Services 入口網站中，選取 [取用]。
2. 在 [取用] 頁面的 [Web 服務取用選項] 區段中，按一下 Excel 圖示。

**使用活頁簿**

1. 開啟活頁簿。
2. 此時會出現安全性警告；請按一下 [啟用編輯] 按鈕。

    ![啟用編輯以移除受保護的檢視安全性警告](./media/consuming-from-excel/enableeditting.png)
3. 此時會出現安全性警告。 按一下 [啟用內容] 按鈕執行試算表上的巨集。

    ![啟用內容以解除停用宏的安全性警告](./media/consuming-from-excel/enablecontent.png)
4. 啟用巨集之後，就會產生表格。 輸入 RRS Web 服務時，必須輸入藍色的欄或 [參數]。 請注意，RRS 服務的輸出：[預測值] 為綠色。 當您填好指定列的所有欄之後，活頁簿會自動呼叫計分 API，並顯示計分的結果。

    ![參數輸入的資料表和產生的預測值](./media/consuming-from-excel/sampletable.png)
5. 若要計算超過一列的分數，請填寫第二列的資料，就會產生預測值。 您甚至可以一次貼上多列。

有了預測值，您就可以使用任何 Excel 功能 (圖表、Power Map、設定格式化的條件等等) 協助將資料視覺化。

## <a name="sharing-your-workbook"></a>共用活頁簿
為了讓巨集能夠運作，試算表會儲存您的 API 金鑰。 這表示您應該只與您信任的實體/個人共用活頁簿。

## <a name="automatic-updates"></a>自動更新
下列兩種情況會產生 RRS 呼叫：

1. 當某一列的所有 [參數]
2. 已在某一列輸入所有 [參數] 且其中任何 [參數] 有變更時。