---
title: 如何建立、匯入和匯出詞彙
description: 瞭解如何在 Azure 範疇中建立、匯入及匯出詞彙條款。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: a693761bcecab87e343014127ad37077c2569e21
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551913"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>如何建立、匯入和匯出詞彙

本文說明如何在 Azure 範疇資料目錄中建立商務詞彙詞彙，以及如何使用 .csv 檔案匯入和匯出詞彙條款。

## <a name="create-a-new-term"></a>建立新詞彙

若要建立新的詞彙字詞，請執行下列步驟：

1. 在首頁上，選取左側導覽中的詞彙圖示，移至 [詞彙清單] 頁面。

2. 在 [ **詞彙條款** ] 頁面上，選取 [ **+ 新增詞彙**]。 頁面隨即開啟，並已選取 **系統預設** 範本。 選擇您想要建立詞彙詞彙的範本，然後選取 [ **繼續**]。

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="新詞彙建立的螢幕擷取畫面。" border="true":::

3. 提供新詞彙的名稱，此名稱在目錄中必須是唯一的。 詞彙名稱會區分大小寫，這表示您可以在目錄中有一個稱為 **sample** 和 **sample** 的詞彙。

4. 新增 **定義**。

5. 設定詞彙的 **狀態** 。 新的條款預設為 **草稿** 狀態。

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="狀態選項的螢幕擷取畫面。" border="true":::

   這些狀態標記是與詞彙相關聯的中繼資料。 您目前可以在每個詞彙上設定下列狀態：

   - **草稿**：此詞彙尚未正式實行。
   - **核准**：此詞彙已正式/標準/核准。
   - 已 **過期**：不應再使用這個字詞。
   - **警示**：此詞彙需要注意。

6. 新增 **資源** 和 **縮略** 字。 如果詞彙是階層的一部分，您可以在 [總覽] 索引標籤中的 **父系** 加入父詞彙。

7. 在 [相關] 索引標籤中新增 **同義字** 和 **相關詞彙** 。

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="新詞彙 > [相關] 索引標籤的螢幕擷取畫面。" border="true":::

8. （選擇性）選取 [ **連絡人** ] 索引標籤，將專家和負責人新增至您的字詞。

9. 選取 [ **建立** ] 以建立您的字詞。

## <a name="import-terms-into-the-glossary"></a>將字詞彙入詞彙

Azure 範疇資料目錄提供可讓您將條款匯入詞彙的範本 .csv 檔案。

您可以匯入目錄中的詞彙。 將會覆寫檔案中的重複字詞。

請注意，詞彙名稱會區分大小寫。 例如， `Sample` 和 `saMple` 都可以存在於相同的詞彙中。

### <a name="to-import-terms-follow-these-steps"></a>若要匯入詞彙，請遵循下列步驟

1. 當您在 [ **詞彙條款** ] 頁面中，選取 [匯 **入詞彙**]。

2. [詞彙範本] 頁面隨即開啟。 將「詞彙」範本與的類型相符。您要匯入的 CSV。

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="[詞彙] 頁面的 [匯入詞彙] 按鈕的螢幕擷取畫面。":::

3. 下載 csv 範本，並使用它來輸入您想要新增的條款。

   > [!Important]
   > 系統只支援匯入範本中可用的資料行。 「系統預設」範本將會有所有的預設屬性。
   > 不過，自訂字詞範本將會有現成的屬性和範本中定義的其他自訂屬性。 因此，CSV 檔案與資料行和資料行名稱的總數不同，視選取的詞彙範本而定。 您也可以在上傳之後，檢查檔案是否有問題。

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="[詞彙條款] 頁面的螢幕擷取畫面，請選取 [要匯入的檔案]。":::

4. 完成填寫 .csv 檔案之後，請選取要匯入的檔案，然後選取 **[確定]**。

5. 系統會上傳檔案，並將所有詞彙新增至您的目錄。

## <a name="export-terms-from-glossary-with-custom-attributes"></a>使用自訂屬性從詞彙匯出詞彙

只要選取的詞彙屬於相同的字詞範本，您就應該能夠從詞彙匯出詞彙。

1. 當您在詞彙中時，預設會停用 [ **匯出** ] 按鈕。 當您選取要匯出的條款之後，如果選取的詞彙屬於相同的範本，則會啟用 [ **匯出** ] 按鈕。

2. 選取 [ **匯出** ] 以下載選取的條款。

## <a name="next-steps"></a>後續步驟

遵循 [教學課程：建立和匯入詞彙](tutorial-import-create-glossary-terms.md) ，以深入瞭解。
