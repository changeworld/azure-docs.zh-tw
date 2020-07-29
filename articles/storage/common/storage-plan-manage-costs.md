---
title: 規劃和管理 Azure 儲存體的成本
description: 瞭解如何使用 Azure 入口網站中的成本分析來規劃和管理 Azure 儲存體的成本。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304520"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>規劃和管理 Azure 儲存體的成本

本文說明如何規劃和管理 Azure 儲存體的成本。 首先，您會使用 Azure 定價計算機來協助規劃儲存體成本，然後再新增任何資源。 在您開始使用 Azure 儲存體資源之後，請使用成本管理功能來設定預算和監視成本。 您也可以查看預測的成本並監控支出趨勢，以識別您可能想要採取行動的區域。

請記住，Azure 儲存體的成本只是 Azure 帳單中每月成本的一部分。 雖然本文說明如何規劃和管理 Azure 儲存體的成本，您仍需支付 Azure 訂用帳戶所使用的所有 Azure 服務和資源，包括協力廠商服務。 在您熟悉如何管理 Azure 儲存體的成本之後，您可以套用類似的方法來管理您的訂用帳戶中所使用之所有 Azure 服務的成本。

## <a name="prerequisites"></a>必要條件

成本分析支援不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要檢視成本資料，您至少需要 Azure 帳戶的讀取存取。 如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>建立 Azure 儲存體帳戶之前估計成本

使用[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估成本，然後再建立並開始將資料傳輸到 Azure 儲存體帳戶。

1. 在 [ [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)] 頁面上，選擇 [**儲存體帳戶**] 磚。

2. 向下流覽頁面，並找出估價的 [**儲存體帳戶**] 區段。

3. 從下拉式清單中選擇 [選項]。 

   當您修改這些下拉式清單的值時，成本預估會變更。 該預估會出現在右上角以及估計的底部。 
    
   ![使用成本分析窗格監視成本](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   當您變更 [**類型**] 下拉式清單的值時，也會變更出現在此工作表中的其他選項。 使用 [**詳細資訊**] 區段中的連結，深入瞭解每個選項的意義，以及這些選項如何影響儲存體相關作業的價格。 

4. 修改其餘選項，以查看它們對您的預估的影響。

## <a name="use-budgets-and-cost-alerts"></a>使用預算和成本警示

您可以建立[預算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)來管理成本，以及建立警示，以便在出現異常消費和超支風險時自動通知利害關係人。 警示是以支出為基礎 (相較於預算和成本閾值)。 預算和警示是針對 Azure 訂用帳戶和資源群組所建立，因此在整體成本監視策略中相當有用。 不過，它們的功能可能有限，可以管理個別的 Azure 服務成本，例如 Azure 儲存體的成本，因為它們是設計來追蹤較高層級的成本。

## <a name="monitor-costs"></a>監視成本

當您搭配 Azure 儲存體使用 Azure 資源時，會產生成本。 資源使用量單位成本會因時間間隔（秒、分鐘、小時、天）或單位使用量（位元組、mb 等等）而異。當 Azure 儲存體開始使用時，就會產生成本。 您可以在 [[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)] 窗格的 [Azure 入口網站] 中查看成本。

當您使用成本分析時，可以針對不同的時間間隔，在圖表和資料表中查看 Azure 儲存體成本。 某些範例是依日期、目前和上個月和年。 您也可以根據預算和預測成本來查看成本。 隨著時間切換到較長的視圖，可協助您找出支出趨勢，並查看超支可能發生的位置。 如果您已建立預算，您也可以輕鬆地查看其超出的位置。

若要在成本分析中查看 Azure 儲存體成本：

1. 登入[Azure 入口網站](https://portal.azure.com)。

2. 開啟 [**成本管理 + 計費**] 視窗，從功能表中選取 [**成本管理**]，然後選取 [**成本分析**]。 然後，您可以從 [**範圍**] 下拉式清單中變更特定訂用帳戶的範圍。

   ![使用成本分析窗格監視成本](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. 若只要查看 Azure 儲存體的成本，請選取 [**新增篩選**]，然後選取 [**服務名稱**]。 然後，從清單中選擇 [**儲存體**]。 

   以下範例顯示只 Azure 儲存體的成本：

   ![使用成本分析窗格監視儲存成本](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

在上述範例中，您會看到服務的目前成本。 Azure 區域（位置）和資源群組的成本也會一併顯示。  

## <a name="next-steps"></a>後續步驟

深入瞭解如何使用[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)來管理成本。

請參閱下列文章，以深入瞭解定價與 Azure 儲存體的搭配運作方式：

- [Azure 儲存體總覽定價](https://azure.microsoft.com/pricing/details/storage/)
- [使用保留容量將 Blob 儲存體的成本最佳化](../blobs/storage-blob-reserved-capacity.md)
