---
title: 教學課程：開始使用 Azure Synapse Analytics - 監視您的 Synapse 工作區
description: 在本教學課程中，您將了解如何監視 Synapse 工作區中的活動。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 8c0cdcad9a7803e0d8063362ca62887990045c86
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209809"
---
# <a name="monitor-your-synapse-workspace"></a>監視您的 Synapse 工作區

在本教學課程中，您將了解如何監視 Synapse 工作區中的活動。 您可以監視 SQL、Apache Spark 和管線的目前活動和過往活動。 

## <a name="introduction-to-the-monitor-hub"></a>監視中樞簡介

開啟 Synapse Studio，然後瀏覽至 [監視] 中樞。 在此，您可以查看工作區中所發生之所有活動的歷程記錄，以及目前作用中的活動。 

* 在 [ **整合**] 下，您可以監視管線、觸發程式和整合執行時間。
* 在 [ **活動**] 下，您可以監視 SPARK 和 SQL 活動。 

## <a name="integration"></a>整合

1. 瀏覽至 [整合] > [管線]。 在此檢視中，您可以在每次管線於工作區中執行時看到。 
1. 找出您在上一個步驟中執行的管線，然後按一下其 [管線名稱] 以檢視詳細資料。
1. 按一下 Synapse Studio 頂端附近的 [階層連結列]，然後按一下 [所有管線執行] 以返回上一個檢視。

## <a name="apache-spark-activities"></a>Apache Spark 活動

1. 流覽至 **> Apache Spark 應用程式的活動**。 現在，您可以查看工作區中正在執行或已執行的所有 Spark 應用程式。
1. 尋找不再執行的應用程式，然後按一下其 [應用程式名稱]。 現在您可以看到 Spark 應用程式的詳細資料。
1. 如果您熟悉 Apache Spark，則可以按一下 [Spark 歷程記錄伺服器] 來找到標準的 Apache Spark 歷程記錄伺服器 UI。

## <a name="sql-activities"></a>SQL 活動

1. 流覽至 **> SQL 要求的活動**。
1. 在此檢視中，您可以看到 SQL 要求。
1. 從 **集** 區篩選器中選取要監視的 **集** 區。 現在，您可以看到在該集區的工作區中正在執行或已執行的所有 SQL 要求。
1. 尋找特定的 SQL 要求，並將滑鼠暫留在該項目上。 暫留之後，您會看到 [SQL 指令碼] 圖示隨即出現。
1. 按一下 [SQL 指令碼] 圖示，以查看 SQL 要求的完整文字。

    > [!NOTE] 
    > 在已啟用工作區的專用 SQL 集區 (先前稱為 SQL DW) 中，透過 Synapse Studio 提交的 SQL 要求可以在監視中樞內檢視。 對於所有其他的監視活動，您可以移至 Azure 入口網站中的專用 SQL 集區 (先前稱為 SQL DW) 監視，加以檢視。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索知識中心](get-started-knowledge-center.md)
