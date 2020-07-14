---
title: 什麼是個人化工具？
description: 個人化工具是雲端式 API 服務，可讓您選擇最佳體驗來對使用者展現，進而從其即時行為中學習。
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: cf046ada21c4920ea9e3853668a5928b2ca9f33a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586213"
---
# <a name="what-is-personalizer"></a>什麼是個人化工具？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 個人化工具是雲端式 API 服務，可協助您的用戶端應用程式選擇單一最佳「內容」項目來顯示每個使用者。 此服務會根據您就內容和關係提供的相關集體即時資訊，從內容項目中選取最佳項目。

在您向使用者呈現內容項目之後，您的系統會監視使用者行為並向個人化工具回報獎勵分數，以根據其收到的關係資訊來改善選取最佳內容的能力。

**內容**可以是您想要從中選取以向使用者顯示的任何資訊單位，例如文字、影像、url 或電子郵件。

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>個人化工具如何選取最佳內容項目？

個人化工具會使用 **增強式學習**，根據所有使用者的集體行為和獎勵分數來選取最佳項目 (_動作_)。 動作是內容項目，例如可供選擇的新聞文章、特定電影或產品。

**排名**呼叫會取得動作項目和動作特性及關係特性，以選取頂端動作項目：

* **具有特性的動作** - 具有每個項目特有特性的內容項目
* **關係特性** - 使用者在使用您的應用程式時，其特性、其關係或其環境

排名呼叫會在 [獎勵動作識別碼] 欄位中，傳回內容項目 (__動作__) 的識別碼以對使用者顯示。
向使用者顯示的__動作__會與機器學習模型一起選擇，並嘗試將一段時間內的獎勵總量最大化。

以下是幾個範例案例：

|內容類型|**動作 (具有特性)**|**關係特性**|傳回的獎勵動作識別碼<br>(顯示此內容)|
|--|--|--|--|
|新聞清單|a. `The president...` (national, politics, [text])<br>b. `Premier League ...` (global, sports, [text, image, video])<br> c. `Hurricane in the ...` (regional, weather, [text,image]|裝置新聞讀取自<br>月或季<br>|`The president...`|
|電影清單|1.`Star Wars` (1977, [action, adventure, fantasy], George Lucas)<br>2.`Hoop Dreams` (1994, [documentary, sports], Steve James<br>3.`Casablanca` (1942, [romance, drama, war], Michael Curtiz)|裝置電影觀賞自<br>螢幕大小<br>使用者類型<br>|3. `Casablanca`|
|產品清單|i. `Product A` (3 kg, $$$$, deliver in 24 hours)<br>ii. `Product B` (20 kg, $$, 2 week shipping with customs)<br>iii. `Product C` (3 kg, $$$, delivery in 48 hours)|裝置購物讀取自<br>使用者的消費層<br>月或季|ii. `Product B`|

個人化工具使用了增強式學習，以根據下列組合選取單一最佳動作，也稱為「獎勵動作識別碼」：
* 定型的模型 - 個人化工具服務收到的過去資訊
* 目前資料 - 具有特性的特定動作和關係特性

## <a name="when-to-call-personalizer"></a>個人化工具的呼叫時機

個人化工具的**排名** [API](https://go.microsoft.com/fwlink/?linkid=2092082) 會在您「每次」即時呈現內容時呼叫。 這也稱為**事件**，並以「事件識別碼」加以註記。

個人化工具的**獎勵** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) 可即時呼叫，或延遲以更加符合您的基礎結構。 您可以根據您的業務需求判斷獎勵分數。 獎勵分數介於 0 到 1 之間。 這可以是單一值，例如 1 表示良好，0 表示不良，或由您建立的演算法所產生的數字，其會考慮您的業務目標和計量。

## <a name="personalizer-content-requirements"></a>個人化工具內容需求

當您的內容如下時，請使用個人化工具：

* 具有一組有限的項目 (最多 ~50) 可供選取。 如果您有較大的清單，[請使用建議引擎](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution)將清單縮減為 50 個項目。
* 具有資訊來描述您要排名的內容：「具有特性的動作」和「關係特性」。
* 每天至少有 1 千個內容相關事件，個人化工具才有效用。 如果個人化工具未收到所需的最小流量，則服務會花更長的時間來判斷單一最佳內容項目。

由於個人化工具會以近乎即時的方式使用集體資訊來傳回單一最佳內容項目，因此服務不會：
* 保存和管理使用者設定檔資訊
* 記錄個別使用者的喜好設定或歷程記錄
* 需要已清除且已加上標籤的內容

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>如何針對用戶端應用程式設計和實作個人化工具

1. 針對內容、**_動作_** 及 **_關係_** 進行[設計](concepts-features.md)與規劃。 決定 **獎勵** 分數的獎勵演算法。
1. 您所建立的每個[個人化工具資源](how-to-settings.md)都會被視為 1 個學習迴圈。 迴圈將會接收該內容或使用者體驗的排名和獎勵呼叫。

    |資源類型| 目的|
    |--|--|
    |[新手模式](concept-apprentice-mode.md) `E0`|在不影響現有應用程式的情況下訓練個人化工具模型，然後將線上學習行為部署到實際執行環境|
    |標準，`S0`|實際執行環境中的線上學習行為|
    |免費，`F0`| 在非實際執行環境中試用線上學習行為|

1. 將個人化工具新增至您的應用程式、網站或系統：
    1. 在您的應用程式、網站或系統中，將**排名**呼叫新增至個人化工具，以在向使用者顯示內容之前，判斷單一最佳「內容」項目。
    1. 向使用者顯示單一最佳「內容」項目，也就是傳回的「獎勵動作識別碼」。
    1. 將_商務邏輯_套用至所收集的使用者行為相關資訊，以判斷**獎勵**分數，例如：

    |行為|計算的獎勵分數|
    |--|--|
    |使用者選取了單一最佳「內容」項目 (獎勵動作識別碼)|**1**|
    |使用者選取了其他內容|**0**|
    |在選取單一最佳「內容」項目 (獎勵動作識別碼) 之前，使用者暫停了不明確地隨意捲動|**0.5**|

    1. 新增**獎勵**呼叫，以傳送 0 到 1 之間的獎勵分數
        * 緊接在顯示您的內容之後
        * 或稍後在離線系統中進行
    1. 在使用一段時間之後，利用離線評估來[評估您的迴圈](concepts-offline-evaluation.md)。 離線評估可讓您測試及評估個人化工具服務的效用，而這不會變更您的程式碼或影響使用者體驗。

## <a name="next-steps"></a>後續步驟


* [個人化工具的運作方式](how-personalizer-works.md)
* [什麼是增強式學習？](concepts-reinforcement-learning.md)
* [了解排名要求的功能和動作](concepts-features.md)
* [了解如何判斷獎勵要求的分數](concept-rewards.md)
* [快速入門](sdk-learning-loop.md)
* [教學課程](tutorial-use-azure-notebook-generate-loop-data.md)
* [使用互動式示範](https://personalizationdemo.azurewebsites.net/)
