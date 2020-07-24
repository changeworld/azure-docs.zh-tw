---
title: 如何在 Synapse Studio 中監視 Apache Spark 應用程式
description: 使用 Synapse Studio 來監視您的 Apache Spark 應用程式。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: a4dc2604dbd62da1baa4278ff3463f41337886bf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052503"
---
# <a name="use-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>使用 Synapse Studio （預覽）來監視您的 Apache Spark 應用程式

使用 Azure Synapse 分析，您可以使用 Spark 在工作區中的 Spark 集區上執行筆記本、作業和其他類型的應用程式。

本文說明如何監視您的 Apache Spark 應用程式，讓您留意最新狀態、問題和進度。

## <a name="accessing-the-list-of-apache-spark-applications"></a>存取 Apache Spark 應用程式的清單

若要查看您工作區中的 Apache Spark 應用程式清單，請先[開啟 Synapse Studio](https://web.azuresynapse.net/) ，然後選取您的工作區。

![登入工作區](./media/common/login-workspace.png)

開啟工作區之後，請選取左側的 [**監視**] 區段。

![選取監視中樞](./media/common/left-nav.png)

選取 [ **Apache Spark 應用程式**]，以查看 Apache Spark 應用程式的清單。

 ![選取 Spark 應用程式](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-apache-spark-applications"></a>篩選您的 Apache Spark 應用程式

您可以篩選 Apache Spark 應用程式清單，以瞭解您感興趣的應用程式。 畫面頂端的篩選準則可讓您指定您想要篩選的欄位。

例如，您可以篩選此視圖，只查看包含 "sales" 名稱的 Apache Spark 應用程式：

![[篩選] 按鈕](./media/common/filter-button.png)

![範例篩選](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-apache-spark-application"></a>查看特定 Apache Spark 應用程式的詳細資料

若要查看您其中一個 Apache Spark 應用程式的詳細資料，請選取 [Apache Spark] 應用程式並查看詳細資料。 如果 Apache Spark 應用程式仍在執行中，您可以監視進度。 [閱讀其他資訊](apache-spark-applications.md)。

## <a name="next-steps"></a>後續步驟

如需有關監視管線執行的詳細資訊，請參閱[監視管線執行 Synapse Studio](how-to-monitor-pipeline-runs.md)一文。 

如需有關 Apache Spark 應用程式的偵錯工具詳細資訊，請參閱[Monitor Apache Spark applications On Synapse Studio](apache-spark-applications.md)一文。