---
title: 監視器導管執行 Azure 同步工作室(預覽版)
description: 使用 Azure 同步工作室監視工作區管道運行。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430782"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>使用 Azure 同步工作室監視工作區導管

借助 Azure Synapse 分析,您可以建立複雜的管道,以便自動協調解決方案中的數據行動、資料轉換和計算活動。 您可以使用 Azure 同步工作室(預覽)創作和監視這些管道。

本文介紹如何監視管道運行,這允許您關注管道的最新狀態、問題和進度。

## <a name="access-the-list-of-pipeline-runs"></a>存取導管執行清單

要查看工作區中運行的管道清單,請首先打開[Azure 同步工作室](https://web.azuresynapse.net/)並選擇工作區。

![登入工作區](./media/common/login-workspace.png)

打開工作區后,選擇左側的 **「監視器」** 部分。

![選擇監視器中心](./media/common/left-nav.png)

選擇 **「管道執行」** 以檢視管道執行的清單。

![選擇導管執行](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>篩選導管執行

您可以篩選管道執行到您感興趣的管道清單。 螢幕頂部的篩選器允許您指定要篩選的欄位。

例如,您可以篩選檢視以檢視僅執行名為「假日」的管道的管道:

![[篩選] 按鈕](./media/common/filter-button.png)

![樣品過濾器](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>檢視有關特定導管執行的詳細資訊

要查看有關管道運行的詳細資訊,請選擇管道運行。 然後查看與管道運行關聯的活動運行。 如果管道仍在運行,您可以監視進度。 
  
## <a name="next-steps"></a>後續步驟

要瞭解有關監視應用程式的更多資訊,請參閱監視器[Apache Spark 應用程式](how-to-monitor-spark-applications.md)一文。 
