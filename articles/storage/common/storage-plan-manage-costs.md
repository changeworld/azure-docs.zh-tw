---
title: 計畫和管理 Azure 存儲的成本
description: 瞭解如何在 Azure 門戶中使用成本分析來規劃和管理 Azure 存儲的成本。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304520"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>計畫和管理 Azure 存儲的成本

本文介紹如何規劃和管理 Azure 存儲的成本。 首先，在添加任何資源之前，使用 Azure 定價計算機説明規劃存儲成本。 開始使用 Azure 存儲資源後，使用成本管理功能來設置預算並監視成本。 您還可以查看預測的成本並監控支出趨勢，以確定您可能想要採取行動的領域。

請記住，Azure 存儲的成本只是 Azure 帳單中每月成本的一部分。 儘管本文介紹了如何規劃和管理 Azure 存儲的成本，但會為用於 Azure 訂閱的所有 Azure 服務和資源（包括協力廠商服務）收費。 熟悉 Azure 存儲的管理成本後，可以應用類似的方法來管理訂閱中使用的所有 Azure 服務的成本。

## <a name="prerequisites"></a>Prerequisites

成本分析支援不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要檢視成本資料，您至少需要 Azure 帳戶的讀取存取。 如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>在創建 Azure 存儲帳戶之前估計成本

在創建資料並開始將資料傳輸到 Azure 存儲帳戶之前，使用[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)估計成本。

1. 在[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)頁上，選擇 **"存儲帳戶"** 磁貼。

2. 向下滾動頁面並找到估算的 **"存儲帳戶"** 部分。

3. 從下拉清單中選擇選項。 

   當您修改這些下拉清單的值時，成本估計將發生變化。 該估計值出現在上角和估計的底部。 
    
   ![使用成本分析窗格監控成本](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   當您更改 **"類型**"下拉清單的值時，此工作表上顯示的其他選項也會更改。 使用 **"更多資訊**"部分中的連結詳細瞭解每個選項的含義以及這些選項如何影響與存儲相關的操作的價格。 

4. 修改其餘選項以查看它們對估計值的影響。

## <a name="use-budgets-and-cost-alerts"></a>使用預算和成本警示

您可以建立[預算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)來管理成本，以及建立警示，以在出現異常消費和超支風險時自動通知利害關係人。 警示是以支出為基礎 (相較於預算和成本閾值)。 為 Azure 訂閱和資源組創建預算和警報，因此它們作為總體成本監視策略的一部分非常有用。 但是，它們可能具有管理單個 Azure 服務成本（如 Azure 存儲成本）的有限功能，因為它們旨在跟蹤更高級別的成本。

## <a name="monitor-costs"></a>監控成本

將 Azure 資源與 Azure 存儲一起使用時，會產生成本。 資源使用單位成本因時間間隔（秒、分鐘、小時和天）或單位使用方式（位元組、百萬位元組等）而異。一旦開始使用 Azure 存儲，就會產生成本。 您可以在 Azure 門戶中[的成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)窗格中查看成本。

使用成本分析時，可以在不同時間間隔的圖形和表中查看 Azure 存儲成本。 一些示例按天、當前和上月以及年份進行。 您還可以根據預算和預測成本查看成本。 隨時間轉換到較長的視圖可以説明您確定支出趨勢，並瞭解支出可能發生在哪裡。 如果您已創建預算，您還可以輕鬆查看超出預算的超出位置。

要查看成本分析中的 Azure 存儲成本：

1. 登錄到[Azure 門戶](https://portal.azure.com)。

2. 打開**成本管理 + 計費**視窗，從功能表中選擇**成本管理**，然後選擇**成本分析**。 然後，可以從 **"範圍"** 下拉清單中更改特定訂閱的範圍。

   ![使用成本分析窗格監控成本](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. 要僅查看 Azure 存儲的成本，請選擇 **"添加篩選器**"，然後選擇 **"服務名稱**"。 然後，從清單中選擇**存儲**。 

   下面是一個示例，顯示僅 Azure 存儲的成本：

   ![使用成本分析窗格監控存儲成本](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

在前面的示例中，您可以看到服務的目前成本。 還會顯示按 Azure 區域（位置）和資源組顯示的成本。  

## <a name="next-steps"></a>後續步驟

詳細瞭解通過[成本分析管理成本](../../cost-management-billing/costs/quick-acm-cost-analysis.md)。

請參閱以下文章，瞭解有關定價如何與 Azure 存儲配合使用的文章：

- [Azure 存儲概述定價](https://azure.microsoft.com/pricing/details/storage/)
- [使用保留容量將 Blob 儲存體的成本最佳化](../blobs/storage-blob-reserved-capacity.md)
