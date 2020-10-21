---
title: 透過使用情況檔案樞紐分析表對 Azure EA 計費問題進行疑難排解
description: 本文可協助您使用從 CSV 使用情況檔案建立的樞紐分析表，對 Enterprise 合約 (EA) 計費問題進行疑難排解。
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: b32bb979176af8a8a3751db8edf9c129caf1002e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131984"
---
# <a name="troubleshoot-ea-billing-issues-with-usage-file-pivot-tables"></a>透過使用情況檔案樞紐分析表對 EA 計費問題進行疑難排解

本文可協助您透過使用情況檔案中的樞紐分析表，對 EA 計費問題進行疑難排解。 Azure 使用情況檔案包含您所有的 Azure 使用情況和使用量資訊。 檔案中的資訊可協助您了解：

- 了解 Azure 保留的使用和套用方式
- 將 Azure 成本管理中的資訊與您的計費發票協調
- 對成本尖峰進行疑難排解
- 計算服務等級協定的退款金額

您可以利用使用情況檔案中的資訊，進一步了解使用問題並加以診斷。 使用情況檔案會以逗號分隔 (CSV) 格式產生。 由於使用情況檔案可能是大型 CSV 檔案，因此在 Excel 之類的試算表應用程式中以樞紐分析表的形式操作和檢視，會較為容易。 本文中的範例使用 Excel，但是您可以使用任何偏好的試算表應用程式。

只有 EA 管理員、帳戶擁有者和部門管理員具有下載使用情況檔案的存取權。

## <a name="get-the-data-and-format-it"></a>取得資料並將其格式化

由於 Azure 使用情況檔案是 CSV 格式，您必須準備資料以在 Excel 中使用。 請使用下列步驟，將資料格式化為資料表。

1. 使用[下載 EA 客戶的使用量](./download-azure-invoice-daily-usage-date.md#download-usage-for-ea-customers)中的指示，下載含有所有費用 (使用量及購買) 檔案的使用量詳細資料第 2 版。
1. 在 Excel 中開啟檔案。
1. 未格式化的資料如下列範例所示。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" :::
1. 選取資料表中的第一個欄位，也就是包含第一個資料行 **BillingAccountID** 的欄位。
1. 按 Ctrl + Shift + 向下鍵，然後按 Ctrl + Shift + 向右鍵，以選取資料表中的所有資訊。
1. 在頂端功能表中，選取 [插入] > [資料表]。 在 [建立資料表] 方塊中，選取 [有標題的表格]，然後選取 [確定]。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="顯示 Excel 未格式化資料的範例" :::
1. 在頂端功能表中，選取 [插入] > [樞紐分析表]，然後選取 [確定]。 動作會在檔案中建立新的工作表。 這會將您導向至工作表右側的樞紐分析表區域。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

[樞紐分析表欄位] 區域是拖放區域。 繼續進行下一節以建立樞紐分析表。

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>建立樞紐分析表以依資源檢視 Azure 成本

在本節中，您會建立樞紐分析表，在此處您可以對整體的一般 Azure 使用情況進行疑難排解。 範例資料表可協助您調查哪個服務使用了最多資源。 或者，您可以檢視產生最多成本的資源，並檢視服務的收費情形。

1. 在 [樞紐分析表欄位] 區域中，將 [計量類別] 和 [產品] 拖曳至 [資料列] 區段。 將 [產品] 放在 [計量類別] 底下。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. 接著，將 [成本] 資料行新增至 [值] 區段。 您也可以改用 [已取用的數量] 資料行，以取得取用量單位和交易的相關資訊。 例如，GB 和小時。 或者，將成本替換為以不同貨幣 (例如 USD、EUR 和 INR) 表示的交易。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. 現在，您已有用於一般化使用量調查的儀表板。 您可以使用樞紐分析表中的篩選選項來篩選特定的服務。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    若要篩選樞紐分析表中的第二個層級 (例如資源)，請選取資料表中的第二層項目。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. 將 [ResourceID] 資料行拖曳至 [產品] 底下的 [資料列] 區域，以依資源查看每項服務的成本。 若要檢視詳細的定價資訊，請檢視組織的「單價」，並在價目表的第一個資料行中搜尋**產品**。
1. 將 [日期] 資料行新增至 [資料行] 區域，以查看產品的每日取用量。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. 使用 **+** 符號，對每個月份的資料行展開及摺疊月份。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::  
    在 [值] 區域中新增 [成本] 和 [數量] 資料行是選擇性的作業。 若這麼做，當 [日期] 資料行位於樞紐分析表的 [資料行] 區段時，每個月和每天底下的每個資料區段中就會建立兩個資料行。
1. 如需其他篩選器，您可以將 SubscriptionID、Department、ResourceGroup、Tags 或 Cost Center 資料行新增至 [篩選器] 區段，並選取您要的項目。

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>建立樞紐分析表以檢視特定資源的成本

單一資源可能會對不同的服務產生數個費用。 例如，虛擬機器可能會產生計算費用、OS 授權、頻寬 (資料傳輸)、RI 使用量，以及快照集的儲存體。 當您想要檢閱特定資源的整體使用情況時，下列步驟將可引導您建立儀表板，以透過您的使用情況檔案來檢視整體使用情況。

1. 在右側功能表中，將 **ResourceID** 拖曳至樞紐分析表功能表中的 [篩選] 區段。
1. 選取您要查看其成本的資源。 在 [搜尋] 方塊中輸入，以尋找資源名稱。
1. 將 [計量類別] 和 [產品] 新增至 [資料列] 區段。 將 [產品] 放在 [計量類別] 底下。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. 接著，將 [成本] 資料行新增至 [值] 區段。 您也可以改用 [已取用的數量] 資料行，以取得取用量單位和交易的相關資訊。 例如，GB 和小時。 或者，將成本替換為以不同貨幣 (例如 USD、EUR 和 INR) 表示的交易。 現在您已有儀表板會顯示資源所取用的所有服務。
1. 將 [日期] 資料行新增至 [資料行] 區段。 其中顯示每日取用量。
1. 您可以在每個月的資料行中使用 **+** 符號來擴充和減少。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="顯示 Excel 未格式化資料的範例" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>後續步驟

- [使用成本分析探索及分析成本](../costs/quick-acm-cost-analysis.md)。