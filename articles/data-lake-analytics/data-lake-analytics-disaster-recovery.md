---
title: Azure Data Lake Analytics 的嚴重損壞修復指引
description: 瞭解如何為您的 Azure Data Lake Analytics 帳戶規劃損毀修復。
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ba231c4a68d92b3f6f35542d739ad9daedd65776
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121396"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics 的嚴重損壞修復指引

Azure Data Lake Analytics 是隨選分析作業服務，可簡化巨量資料。 無需部署、設定及調整硬體，您只需寫入查詢便可轉換資料並擷取寶貴的深入見解。 透過針對所需的功能設定級別，此項分析服務便可立即處理任何規模的工作。 只有在作業進行時您才需要支付費用，十分符合成本效益。 本文提供如何保護您的作業免于罕見全區域中斷或意外刪除的指引。

## <a name="disaster-recovery-guidance"></a>災害復原指引

使用 Azure Data Lake Analytics 時，請務必準備您自己的嚴重損壞修復計畫。 本文可協助引導您建立嚴重損壞修復計畫。 還有其他資源可協助您建立自己的方案：
- [Azure 應用程式的失敗及災害復原](/azure/architecture/reliability/disaster-recovery)
- [Azure 復原技術指導](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>最佳做法和案例指引

您可以在讀取和寫入 U SQL 資料表和非結構化資料的區域中，于 ADLA 帳戶中執行循環性的 U-SQL 作業。  採取下列步驟來準備嚴重損壞：

1. 在次要區域中建立將在中斷期間使用的 ADLA 和 ADLS 帳戶。

   > [!NOTE]
   > 由於帳戶名稱是全域唯一的，因此請使用一致的命名配置來指出哪個帳戶是次要的。

2. 針對非結構化資料，請參閱[Azure Data Lake Storage Gen1 中資料](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)的嚴重損壞修復指引

3. 對於儲存在 ADLA 資料表和資料庫中的結構化資料，建立中繼資料成品的複本，例如資料庫、資料表、資料表值函式和元件。 當生產環境發生變更時，您需要定期重新同步處理這些成品。 例如，新插入的資料必須複寫到次要區域，方法是複製資料並插入至次要資料表。

   > [!NOTE]
   > 這些物件名稱的範圍是次要帳戶，而且不是全域唯一的，因此可以與主要生產帳戶中的名稱相同。

在中斷期間，您必須更新腳本，讓輸入路徑指向次要端點。 然後，使用者會將其工作提交至次要地區中的 ADLA 帳戶。 作業的輸出會寫入次要區域中的 ADLA 和 ADLS 帳戶。

## <a name="next-steps"></a>後續步驟

[Azure Data Lake Storage Gen1 中資料的災害復原指導方針](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
