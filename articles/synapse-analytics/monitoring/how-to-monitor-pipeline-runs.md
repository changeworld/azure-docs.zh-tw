---
title: 監視管線執行 Azure Synapse Studio （預覽）
description: 使用 Azure Synapse Studio 來監視您的工作區管線執行。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430782"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>使用 Azure Synapse Studio 來監視您的工作區管線執行

透過 Azure Synapse 分析，您可以建立複雜的管線，以自動化及協調您的解決方案內的資料移動、資料轉換和計算活動。 您可以使用 Azure Synapse Studio （預覽）來撰寫和監視這些管線。

本文說明如何監視您的管線執行，讓您留意管線的最新狀態、問題和進度。

## <a name="access-the-list-of-pipeline-runs"></a>存取管線執行清單

若要查看您工作區中的管線執行清單，請先[開啟 Azure Synapse Studio](https://web.azuresynapse.net/) ，然後選取您的工作區。

![登入工作區](./media/common/login-workspace.png)

開啟工作區之後，請選取左側的 [**監視**] 區段。

![選取監視中樞](./media/common/left-nav.png)

選取 [**管線執行**] 以查看管線執行的清單。

![選取管線執行](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>篩選您的管線執行

您可以篩選您感興趣的管線執行清單。 畫面頂端的篩選準則可讓您指定您想要篩選的欄位。

例如，您可以篩選此視圖，只查看名為「假日」之管線的管線執行：

![[篩選] 按鈕](./media/common/filter-button.png)

![範例篩選](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>查看特定管線執行的詳細資料

若要查看管線執行的詳細資料，請選取管線執行。 然後，查看與管線執行相關聯的活動執行。 如果管線仍在執行中，您可以監視進度。 
  
## <a name="next-steps"></a>後續步驟

若要深入瞭解監視應用程式，請參閱[監視 Apache Spark 應用程式](how-to-monitor-spark-applications.md)一文。 
