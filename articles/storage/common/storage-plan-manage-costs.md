---
title: 規劃和管理 Azure Blob 儲存體的成本
description: 瞭解如何在 Azure 入口網站中使用成本分析來規劃和管理 Azure Blob 儲存體的成本。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 75b464c140bfda6c3f3559d3bfdbe1e6bc2e7f24
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760731"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>規劃和管理 Azure Blob 儲存體的成本

本文可協助您規劃和管理 Azure Blob 儲存體的成本。 首先，使用 Azure 定價計算機來預估成本。 建立儲存體帳戶之後，請將帳戶優化，讓您只需支付需要的部分。 使用成本管理功能來設定預算和監視成本。 您也可以查看預測的成本，並監視支出趨勢，以識別您可能想要採取行動的區域。

請記住，Blob 儲存體的成本只是 Azure 帳單中每月成本的一部分。 雖然本文說明如何預估和管理 Blob 儲存體的成本，但您會針對 Azure 訂用帳戶所使用的所有 Azure 服務和資源（包括協力廠商服務）支付費用。 在您熟悉如何管理 Blob 儲存體的成本之後，您可以套用類似的方法來管理您的訂用帳戶中所使用之所有 Azure 服務的成本。

## <a name="estimate-costs"></a>預估成本

使用[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估成本，然後再建立並開始將資料傳輸到 Azure 儲存體帳戶。

1. 在 [ [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)] 頁面上，選擇 [**儲存體帳戶**] 磚。

2. 向下流覽頁面，並找出估價的 [**儲存體帳戶**] 區段。

3. 從下拉式清單中選擇 [選項]。 

   當您修改這些下拉式清單的值時，成本預估會變更。 該預估會出現在右上角以及估計的底部。 
    
   ![使用成本分析窗格監視成本](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   當您變更 [**類型**] 下拉式清單的值時，也會變更出現在此工作表中的其他選項。 使用 [**詳細資訊**] 區段中的連結，深入瞭解每個選項的意義，以及這些選項如何影響儲存體相關作業的價格。 

4. 修改其餘選項，以查看它們對您的預估的影響。

## <a name="optimize-costs"></a>將成本優化

請考慮使用這些選項來降低成本。 

- 保留儲存體容量

- 將資料組織成存取層

- 在存取層之間自動移動資料

本節將更詳細地說明每個選項。 

#### <a name="reserve-storage-capacity"></a>保留儲存體容量

您可以使用 Azure 儲存體保留容量，來節省 blob 資料的儲存成本費用。 Azure 儲存體保留容量會在您認可為一年或三年的保留時，為您提供區塊 blob 容量和標準儲存體帳戶中 Azure Data Lake Storage Gen2 資料的折扣。 保留會針對保留期提供固定的儲存容量量。 Azure 儲存體保留容量可以大幅降低區塊 blob 和 Azure Data Lake Storage Gen2 資料的容量成本。 

若要深入瞭解，請參閱[使用保留容量將 Blob 儲存體的成本優化](https://docs.microsoft.com/azure/storage/blobs/storage-blob-reserved-capacity)。

#### <a name="organize-data-into-access-tiers"></a>將資料組織成存取層

您可以藉由將 blob 資料放入最符合成本效益的存取層來降低成本。 從三個階層中選擇，其設計目的是要將資料使用的成本優化。 例如，經常性存取*層具有較高的儲存*成本，但存取成本較低。 因此，如果您打算經常存取資料，熱層可能是最符合成本效益的選擇。 如果您計畫較不頻繁地存取資料，*冷*或*封存*層可能會產生最大的意義，因為它會提高存取資料的成本，同時降低儲存資料的成本。    

若要深入瞭解，請參閱[Azure Blob 儲存體：經常性、非經常性和封存存取層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)。

#### <a name="automatically-move-data-between-access-tiers"></a>在存取層之間自動移動資料

使用生命週期管理原則，定期在各層之間移動資料，以節省最多費用。 這些原則可以使用您指定的規則，將資料移至。 例如，您可以建立一個規則，將 blob 移至封存層（如果該 blob 在90天內未修改）。 藉由建立原則來調整資料的存取層，您可以針對您的需求設計成本最低的儲存選項。

若要深入瞭解，請參閱[管理 Azure Blob 儲存體生命週期](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

## <a name="create-budgets"></a>建立預算

您可以建立[預算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)來管理成本，以及建立警示，以便在出現異常消費和超支風險時自動通知利害關係人。 警示是以支出為基礎 (相較於預算和成本閾值)。 預算和警示是針對 Azure 訂用帳戶和資源群組所建立，因此在整體成本監視策略中相當有用。 不過，它們的功能可能有限，可以管理個別的 Azure 服務成本，例如 Azure 儲存體的成本，因為它們是設計來追蹤較高層級的成本。

## <a name="monitor-costs"></a>監視成本

當您搭配 Azure 儲存體使用 Azure 資源時，會產生成本。 資源使用量單位成本會因時間間隔而異 (秒、分鐘、小時、天) 或單位使用量 (位元組、mb 等等。 ) 成本會在 Azure 儲存體開始使用時產生。 您可以在 [[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)] 窗格的 [Azure 入口網站] 中查看成本。

當您使用成本分析時，可以針對不同的時間間隔，在圖表和資料表中查看 Azure 儲存體成本。 某些範例是依日期、目前和上個月和年。 您也可以根據預算和預測成本來查看成本。 隨著時間切換到較長的視圖，可協助您找出支出趨勢，並查看超支可能發生的位置。 如果您已建立預算，您也可以輕鬆地查看其超出的位置。

>[!NOTE]
> 成本分析支援不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要檢視成本資料，您至少需要 Azure 帳戶的讀取存取。 如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../../cost-management-billing/costs/assign-access-acm-data.md)。

若要在成本分析中查看 Azure 儲存體成本：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 開啟 [**成本管理 + 計費**] 視窗，從功能表中選取 [**成本管理**]，然後選取 [**成本分析**]。 然後，您可以從 [**範圍**] 下拉式清單中變更特定訂用帳戶的範圍。

   ![使用成本分析窗格監視成本](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. 若只要查看 Azure 儲存體的成本，請選取 [**新增篩選**]，然後選取 [**服務名稱**]。 然後，從清單中選擇 [**儲存體**]。 

   以下範例顯示只 Azure 儲存體的成本：

   ![使用成本分析窗格監視儲存成本](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

在上述範例中，您會看到服務的目前成本。 Azure 區域的成本會 (位置) 和資源群組也會出現。 您也可以新增其他篩選 (例如：查看特定儲存體帳戶的成本) 的篩選準則。

## <a name="next-steps"></a>後續步驟

深入瞭解如何使用[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)來管理成本。

請參閱下列文章，以深入瞭解定價與 Azure 儲存體的搭配運作方式：

- [Azure 儲存體總覽定價](https://azure.microsoft.com/pricing/details/storage/)
- [使用保留容量將 Blob 儲存體的成本最佳化](../blobs/storage-blob-reserved-capacity.md)
