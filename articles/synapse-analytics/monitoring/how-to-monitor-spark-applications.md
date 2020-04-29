---
title: 監視 Apache Spark 應用程式
description: 使用 Azure Synapse Studio 來監視您的 Apache Spark 應用程式。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430743"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>使用 Azure Synapse Studio （預覽）來監視您的 Apache Spark 應用程式

使用 Azure Synapse 分析，您可以使用 Spark 在工作區中的 Spark 集區上執行筆記本、作業和其他類型的應用程式。

本文說明如何監視您的 Spark 應用程式，讓您留意最新狀態、問題和進度。

## <a name="accessing-the-list-of-spark-applications"></a>存取 Spark 應用程式清單

若要查看您工作區中的 Spark 應用程式清單，請先[開啟 Azure Synapse Studio](https://web.azuresynapse.net/) ，然後選取您的工作區。

  > [!div class="mx-imgBorder"]
  > ![登入工作區](./media/common/login-workspace.png)

開啟工作區之後，請選取左側的 [**監視**] 區段。

  > [!div class="mx-imgBorder"]
  > ![選取監視中樞](./media/common/left-nav.png)

選取 [ **spark 應用程式**] 以查看 spark 應用程式的清單。

  > [!div class="mx-imgBorder"]
  > ![選取 Spark 應用程式](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>篩選您的 Spark 應用程式

您可以將 Spark 應用程式的清單篩選為您感興趣的應用程式。 畫面頂端的篩選準則可讓您指定您想要篩選的欄位。

例如，您可以篩選此視圖，只查看包含名稱為 "sales" 的 Spark 應用程式：

  > [!div class="mx-imgBorder"]
  > ![[篩選] 按鈕](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![範例篩選](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>查看特定 Spark 應用程式的詳細資料

若要查看其中一個 Spark 應用程式的詳細資料，請選取 Spark 應用程式並查看詳細資料。 如果 Spark 應用程式仍在執行中，您可以監視進度。

## <a name="next-steps"></a>後續步驟

如需有關監視管線執行的詳細資訊，請參閱[監視管線執行 Azure Synapse Studio](how-to-monitor-pipeline-runs.md)文章。  
