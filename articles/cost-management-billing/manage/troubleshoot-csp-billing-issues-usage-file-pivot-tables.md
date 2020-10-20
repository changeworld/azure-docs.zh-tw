---
title: 透過使用情況檔案樞紐分析表對 Azure CSP 計費問題進行疑難排解
description: 本文可協助您使用從 CSV 使用情況檔案建立的樞紐分析表，對 Azure 雲端解決方案提供者 (CSP) 計費問題進行疑難排解。
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026280"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>透過使用情況檔案樞紐分析表對 CSP 計費問題進行疑難排解

本文可協助您使用合作夥伴中心對帳 (使用情況) 檔案中的樞紐分析表，對雲端解決方案提供者 (CSP) 計費問題進行疑難排解。 Azure 使用情況檔案包含您所有的 Azure 使用情況和使用量資訊。 檔案中的資訊可協助您了解：

- 了解 Azure 保留的使用和套用方式
- 將 Azure 成本管理中的資訊與您的計費發票協調
- 對成本尖峰進行疑難排解
- 計算服務等級協定的退款金額

您可以利用使用情況檔案中的資訊，進一步了解使用問題並加以診斷。 使用情況檔案會以逗號分隔 (CSV) 格式產生。 由於使用情況檔案可能是大型 CSV 檔案，因此在 Excel 之類的試算表應用程式中以樞紐分析表的形式操作和檢視，會較為容易。 本文中的範例使用 Excel，但是您可以使用任何偏好的試算表應用程式。

只有計費管理員和全域管理員才有權下載對帳檔案。 如需詳細資訊，請參閱[了解如何閱讀合作夥伴中心對帳檔案中的明細項目](/partner-center/use-the-reconciliation-files)。

## <a name="get-the-data-and-format-it"></a>取得資料並將其格式化

由於 Azure 使用情況檔案是 CSV 格式，您必須準備資料以在 Excel 中使用。 請使用下列步驟，將資料格式化為資料表。

1. 使用[尋找您的帳單](/partner-center/read-your-bill#find-your-bill)中的指示，下載使用情況檔案。
1. 在 Excel 中開啟檔案。
1. 未格式化的資料如下列範例所示。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. 選取資料表中的第一個欄位 **PartnerID**。
1. 按 Ctrl + Shift + 向下鍵，然後按 Ctrl + Shift + 向右鍵，以選取資料表中的所有資訊。
1. 在頂端功能表中，選取 [插入] > [資料表]。 在 [建立資料表] 方塊中，選取 [有標題的表格]，然後選取 [確定]。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="顯示 Excel 未格式化資料的範例" :::
1. 在頂端功能表中，選取 [插入] > [樞紐分析表]，然後選取 [確定]。 動作會在檔案中建立新的工作表，並將您導向至工作表右側的樞紐分析表區域。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

[樞紐分析表欄位] 區域是拖放區域。 繼續進行下一節以建立樞紐分析表。

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>建立樞紐分析表以依資源檢視 Azure 成本

在本節中，您會建立樞紐分析表，在此處您可以對整體的一般 Azure 使用情況進行疑難排解。 範例資料表可協助您調查哪個服務使用了最多資源。 或者，您可以檢視產生最多成本的資源，並查看服務的收費情形。

1. 在 [樞紐分析表欄位] 區域中，將 [服務名稱] 和 [資源] 拖曳至 [資料列] 區域。 將 [資源] 放在 [服務名稱] 底下。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. 接著，將 [稅後總計] 放在 [值] 區域中。 您也可以改用 [已取用的數量] 資料行，以取得取用量單位和交易的相關資訊。 例如，GB 和小時。 或者，將成本替換為以不同貨幣 (例如 USD、EUR 和 INR) 表示的交易。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. 現在，您已有用於一般化使用量調查的儀表板。 您可以使用樞紐分析表中的篩選選項來篩選特定的服務。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    若要篩選樞紐分析表中的第二個層級 (例如資源)，請選取資料表中的第二層項目。
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. 如需其他篩選器，您可以將 [SubscriptionID] 和 [客戶公司名稱] 新增至 [篩選] 區域，然後選取所需的範圍。

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>建立樞紐分析表以依日期檢視 Azure 使用情況

在本節中，您會建立樞紐分析表，在此處您可以依據已取用的數量和日期對整體的一般 Azure 使用情況進行疑難排解。 依日期和服務識別計費尖峰是很有用的。 或者，您可以檢視產生最多成本的資源，並查看服務的收費情形。

您的對帳檔案有兩個資料表。 一個位於頂端 (主資料表)，另一個資料表則位於文件底部。 第二個數據表有許多相同的資訊，但不包含定價或成本詳細資料。 其中包含使用日期和已取用的數量。

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. 使用[取得資料並將其格式化](#get-the-data-and-format-it)一節中的相同步驟，以對帳檔案底部的資訊建立 Excel 資料表。
1. 當資料表準備就緒，且您擁有樞紐分析表工作表後，請使用「建立樞紐分析表以依資源檢視 Azure 成本」一節中的相同步驟準備儀表板。 請將 [已取用的數量] 放在 [值] 區域中，而不要使用稅後總計。
1. 將 [使用日期] 新增至 [資料行] 區段。 樞紐資料表應該會如下列範例所示。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. 現在，您已有儀表板會顯示每個日期的使用量。 您可以選取 **+** 符號來擴充每個月份。

儀表板會以 GB、小時和傳輸次數為單位顯示已取用的數量。

若要檢視每日的價格，您可以將 [資源 GUID] 新增至 [資料列] 區域。 在上方資料表中，新增資源的單價 (**ListPrice**)。 將 **ListPrice** 乘以 [已取用的數量]，計算您的稅前費用。 數量應符合。

某些資源 (服務) 已透過已取用的數量調整定價。 例如，某些資源在取用前 100 GB 時的價格較高，之後使用的 GB 數價格較低。 以手動方式計算成本時，請留意調整的定價。

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>建立樞紐分析表以檢視特定資源的成本

單一資源可能會對不同的服務產生數個費用。 例如，虛擬機器可能會產生計算費用、OS 授權、頻寬 (資料傳輸)、RI 使用量，以及快照集的儲存體。 當您想要檢閱特定資源的整體使用情況時，下列步驟將可引導您建立儀表板，以透過您的使用情況檔案來檢視整體使用情況。

對帳檔案不包含資源特定的詳細資料。 因此，您應使用彙總的使用情況檔案。 請連絡 [Azure 計費支援](https://go.microsoft.com/fwlink/?linkid=2083458)，向他們索取您訂用帳戶的彙總使用情況檔案。 彙總檔案會在訂用帳戶層級產生。 未格式化的資料如下列範例所示。

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

該檔案包含下列資料行。

- **UsageStart** 和 **UsageEnd** - 每個明細項目的日期 (每個單位的使用量)。 例如，每天。
- **MeteredResourceID** - 在 Azure 中，這會對應至計量識別碼。
- **屬性** - 包含具有其他詳細資料 (例如位置) 的執行個體識別碼 (資源名稱)。
- **數量** - 對帳檔案中已取用的數量。

1. 選取資料表中的第一個欄位 **PartnerID**。  
1. 按 Ctrl + Shift + 向下鍵，然後按 Ctrl + Shift + 向右鍵，以選取資料表中的所有資訊。
1. 在頂端功能表中，選取 [插入] > [資料表]。 在 [建立資料表] 方塊中，選取 [有標題的表格]，然後選取 [確定]。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="顯示 Excel 未格式化資料的範例" :::
1. 在頂端功能表中，選取 [插入] > [樞紐分析表]，然後選取 [確定]。 動作會在檔案中建立新的工作表，並將您導向至工作表右側的樞紐分析表區域。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. 接著，將 [MeteredResourceID] 新增至 [資料列] 區域，並將 [數量] 新增至 [值]。 結果會顯示整體使用情況的資訊。 如需其他詳細資料，請將 [UsageEndDateTime] 放在 [資料行] 區域中。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="顯示 Excel 未格式化資料的範例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. 若要檢視整體報告，請將 [屬性] 新增至 [資料列] 中的 [MeteredResourceID] 下。 其中顯示使用情況的完整儀表板。
1. 若要依特定資源進行篩選，請將 [屬性] 新增至 [篩選] 區域，然後選取所需的使用情況。 您可以使用 [搜尋] 來尋找資源名稱。
    若要檢視資源的成本，請尋找已取用的數量總計，並將其值乘以定價。 每個資源 GUID (MeteredResourceID) 有不同的定價。 如果資源取用數個 MeteredResourceID，您必須記下每個識別碼的總計值。

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>後續步驟

- [開始使用適用於合作夥伴的 Azure 成本管理](../costs/get-started-partners.md)。