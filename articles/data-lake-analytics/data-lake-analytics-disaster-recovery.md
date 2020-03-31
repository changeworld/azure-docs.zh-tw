---
title: Azure 資料湖分析的災害復原指南
description: 瞭解如何規劃 Azure 資料湖分析帳戶的災害復原。
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889770"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure 資料湖分析的災害復原指南

Azure Data Lake Analytics 是隨選分析作業服務，可簡化巨量資料。 無需部署、設定及調整硬體，您只需寫入查詢便可轉換資料並擷取寶貴的深入見解。 透過針對所需的功能設定級別，此項分析服務便可立即處理任何規模的工作。 只有在作業進行時您才需要支付費用，十分符合成本效益。 本文提供有關如何保護作業免受罕見區域範圍中斷或意外刪除的指導。

## <a name="disaster-recovery-guidance"></a>災害復原指引

使用 Azure 資料湖分析時，準備自己的災害復原計畫至關重要。 本文説明您構建災害復原計畫。 還有其他資源可以説明您創建自己的計畫：
- [Azure 應用程式的失敗及災害復原](/azure/architecture/reliability/disaster-recovery)
- [Azure 復原技術指導](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>最佳實踐和方案指南

您可以在讀取和寫入 U-SQL 表以及非結構化資料的區域中的 ADLA 帳戶中運行定期 U-SQL 作業。  通過執行以下步驟為災難做好準備：

1. 在中斷期間將使用的次要區域中創建 ADLA 和 ADLS 帳戶。

   > [!NOTE]
   > 由於帳戶名稱全域是唯一的，請使用一致的命名方案，指示哪個帳戶是次要的。

2. 對於非結構化資料，請參閱[Azure 資料湖存儲第 1 代中的資料的災害復原指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. 對於存儲在 ADLA 表和資料庫中的結構化資料，創建中繼資料專案的副本，如資料庫、表、表值函數和程式集。 當生產中發生更改時，您需要定期重新同步這些工件。 例如，新插入的資料必須通過複製資料並插入到輔助表中複製到次要區域。

   > [!NOTE]
   > 這些物件名稱的範圍限定為輔助帳戶，並且不是全域唯一的，因此它們可以具有與主生產帳戶中相同的名稱。

在中斷期間，您需要更新腳本，以便輸入路徑指向輔助終結點。 然後，使用者將其作業提交到次要區域中的 ADLA 帳戶。 然後，作業的輸出將寫入次要區域中的 ADLA 和 ADLS 帳戶。

## <a name="next-steps"></a>後續步驟

[Azure Data Lake Storage Gen1 中資料的災害復原指導方針](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
