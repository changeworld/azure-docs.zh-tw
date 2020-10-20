---
title: 包含檔案
description: 包含檔案
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026239"
---
## <a name="transform-data-before-using-large-usage-files"></a>使用大型使用情況檔案之前先轉換資料

有時，您的使用情況或對帳檔案會太大而無法開啟。 或者，您可能只需使用部分資訊來排解問題。 例如，您可能只需要特定資源的資訊，或只需某些服務或資源群組的取用量資訊。 您可以在建立樞紐分析表之前，先轉換資料加以摘要說明。

1. 在 Excel 中開啟空白的活頁簿。
1. 在上方的功能表中，選取 [資料] > [從文字/CSV]，選取您的使用情況檔案，然後選取 [匯入]。
1. 在視窗底部，選取 [轉換資料]。 新的視窗會顯示資料的摘要。  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="顯示摘要資料的範例" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. 如果您有 Microsoft 客戶合約，請略過此步驟並繼續下一步，因為 MCA 使用情況檔案的第一個資料列中通常會有資料行標題。 藉由建立資料表來準備資料。 移除前幾個資料列，僅保留標題。 選取 [移除資料列] > [移除頂端資料列]。  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="顯示摘要資料的範例" :::
1. 在 [移除頂端資料列] 視窗中，輸入要在頂端移除的資料列數目。 EA 的數目通常是 2，CSP 則通常是 1。 選取 [確定]  。
1. 選取 [使用第一個資料列作為標題]。  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="顯示摘要資料的範例" :::
    
    資料表檢視會將資料行標題顯示在最上方。
1. 接著，新增篩選器。 使用每個資料行標題右側的選取器箭號來進行篩選。 建議的篩選器包括訂用帳戶識別碼、服務名稱 (計量類別)、執行個體識別碼、資源群組。 您可以在相同的文件中使用多個篩選器。 建議您套用所有可能的篩選器，以縮減檔案大小並利於後續使用。
1. 套用篩選器之後，請選取 [關閉並載入]。  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="顯示摘要資料的範例" :::

檔案會載入並顯示已篩選使用量資料的資料表。 現在，您可以建立新的樞紐分析表，對使用量問題進行疑難排解。