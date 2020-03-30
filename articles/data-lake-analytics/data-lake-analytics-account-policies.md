---
title: 管理 Azure 資料湖分析帳戶原則
description: 瞭解如何使用帳戶原則來控制資料湖分析帳戶的使用，例如最大 A 和最大作業。
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72966433"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>使用帳戶原則管理 Azure 資料湖分析

帳戶原則可説明您控制如何使用 Azure 資料湖分析帳戶的資源。 這些原則可讓您控制使用 Azure Data Lake Analytics 的成本。 例如，您可以使用這些原則，藉由限制帳戶可以同時使用多少 AU ，來防止非預期的成本尖峰。

## <a name="account-level-policies"></a>帳戶層級原則

這些原則會套用到 Data Lake Analytics 帳戶中的所有作業。

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Data Lake Analytics 帳戶中的 AU 數目上限
此原則控制 Data Lake Analytics 帳戶可以使用的分析單位 (AU) 總數。 根據預設，此值設定為 250。 例如，如果此值設為 250 AU，您可以有一項已指派 250 AU 的執行中作業，或 10 項各有 25 AU 的執行中作業。 其他已提交的作業會排入佇列，直到執行中的作業完成為止。 當執行中的作業完成時，會釋出 AU 以便執行已排入佇列的作業。

若要變更 Data Lake Analytics 帳戶的 AU 數目：

1. 在 Azure 入口網站中，移至您的 Data Lake Analytics 帳戶。
2. 按一下 **"限制"和"策略**"。
3. 在 [AU 上限]**** 之下，移動滑桿以選取值，或在文字方塊中輸入值。 
4. 按一下 [儲存]****。

> [!NOTE]
> 如果您需要的 AU 超過預設值 (250)，請在入口網站中按一下 [說明 + 支援]**** 以提交支援要求。 您可以增加 Data Lake Analytics 帳戶中可用的 AU 數目。
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>可以同時執行的作業數目上限
此策略限制可以同時運行的作業數。 根據預設，此值設定為 20。 如果 Data Lake Analytics 有 AU 可用，則會安排立即執行新作業，直到執行中的作業總數達到此原則的值為止。 當您達到可同時運行的最大作業數時，後續作業按優先順序順序排隊，直到一個或多個正在運行的作業完成（具體取決於可用的 A）。

若要變更可以同時執行的作業數目：

1. 在 Azure 入口網站中，移至您的 Data Lake Analytics 帳戶。
2. 按一下 **"限制"和"策略**"。
3. 在 [執行中作業數目上限]**** 之下，移動滑桿以選取值，或在文字方塊中輸入值。 
4. 按一下 [儲存]****。

> [!NOTE]
> 如果您需要執行的作業數目超過預設值 (20)，請在入口網站中按一下 [說明 + 支援]**** 以提交支援要求。 您可以增加 Data Lake Analytics 帳戶中可以同時執行的作業數目。
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>保留作業中繼資料和資源的時間長度 
當使用者運行 U-SQL 作業時，資料湖分析服務將保留所有相關檔。 這些檔包括 U-SQL 腳本、U-SQL 腳本中引用的 DLL 檔、編譯的資源和統計資訊。 這些檔案位於預設 Azure Data Lake 儲存體帳戶的 /system/ 資料夾中。 此原則控制這些資源在自動刪除前的儲存時間長度 (預設值是 30 天)。 您可以使用這些檔案進行偵錯，以及未來將重新執行之作業的效能微調。

若要變更保留作業中繼資料和資源的時間長度：

1. 在 Azure 入口網站中，移至您的 Data Lake Analytics 帳戶。
2. 按一下 **"限制"和"策略**"。
3. 在 [保留作業查詢的天數]**** 之下，移動滑桿以選取值，或在文字方塊中輸入值。  
4. 按一下 [儲存]****。

## <a name="job-level-policies"></a>作業層級原則

作業級策略允許您控制單個使用者（或特定安全性群組的成員）可以設置的最大 A 和最大優先順序， 這些作業級別策略允許您控制他們提交的作業設置的最大優先順序。 這個原則可讓您控制使用者所產生的成本。 也可讓您控制已排程的作業可能對正在相同 Data Lake Analytics 帳戶中執行之高優先順序生產作業的影響。

您可以在作業層級設定兩個 Data Lake Analytics 原則：

* **每個作業的 AU 限制**：使用者只能提交擁有的 AU 不超過此數目的作業。 根據預設，此限制與帳戶的 AU 上限相同。
* **優先順序**：使用者只能提交優先順序低於或等於此值的作業。 數字較高表示優先順序較低。 根據預設，這個限制會設定為 1，這是最高的可能優先順序。

每個帳戶都已設定預設原則。 預設原則會套用到帳戶的所有使用者。 可以為特定使用者和組創建其他策略。 

> [!NOTE]
> 帳戶層級原則和作業層級原則可同時套用。
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>新增特定使用者或群組的原則

1. 在 Azure 入口網站中，移至您的 Data Lake Analytics 帳戶。
2. 按一下 **"限制"和"策略**"。
3. 在 [作業提交限制]**** 之下，按一下 [新增原則]**** 按鈕。 然後，選取或輸入下列設定：
    1. **計算原則名稱**：輸入原則名稱，藉此提醒您原則的用途。
    2. **選取使用者或群組**：選取適用此原則的使用者或群組。
    3. **設定作業 AU 限制**：設定會套用到所選使用者或群組的 AU 限制。
    4. **設定優先順序限制**：設定會套用所選使用者或群組的優先順序限制。

4. 按一下 [確定]****。

5. 新原則會列在 [預設]**** 原則資料表的 [作業提交限制]**** 之下。 

### <a name="delete-or-edit-an-existing-policy"></a>刪除或編輯現有原則

1. 在 Azure 入口網站中，移至您的 Data Lake Analytics 帳戶。
2. 按一下 **"限制"和"策略**"。
3. 在 [作業提交限制]**** 之下，尋找您想要編輯的原則。
4.  要查看"**刪除**和**編輯**"選項，請在表的最右側列中按一下`...`。

## <a name="additional-resources-for-job-policies"></a>作業原則的其他資源
* [原則概觀部落格文章](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [帳戶層級原則部落格文章](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [作業層級原則部落格文章](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>後續步驟

* [Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* [使用 Azure 入口網站開始使用 Data Lake Analytics](data-lake-analytics-get-started-portal.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)