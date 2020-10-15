---
title: 教學課程：開始使用 Azure Synapse Analytics - 監視您的 Synapse 工作區
description: 在本教學課程中，您將了解如何監視 Synapse 工作區中的活動。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: f8be96fb008471d040839141a230c13b8f1657a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708167"
---
# <a name="monitor-your-synapse-workspace"></a>監視您的 Synapse 工作區

在本教學課程中，您將了解如何監視 Synapse 工作區中的活動。 您可以監視 SQL、Apache Spark 的目前活動和過往活動。 和管線。 

## <a name="introduction-to-the-monitor-hub"></a>監視中樞簡介

開啟 Synapse Studio，然後瀏覽至 [監視] 中樞。 在此，您可以查看工作區中所發生之所有活動的歷程記錄，以及目前作用中的活動。 

* 在 [協調流程] 底下，您可以監視管線、觸發程序和整合執行階段
* 在 [活動] 底下，您可以監視 Spark 和 SQL 活動。 

## <a name="orchestration"></a>協調流程

1. 瀏覽至 [協調流程] > [管線]。 在此檢視中，您可以在每次管線於工作區中執行時看到。 
1. 找出您在上一個步驟中執行的管線，然後按一下其 [管線名稱]。
1. 現在您可以看到該管線內的個別活動如何執行。
1. 按一下 Synapse Studio 頂端附近的 [階層連結列]，然後按一下 [所有管線執行] 以返回上一個檢視。

## <a name="apache-spark-activities"></a>Apache Spark 活動

1. 瀏覽至 [協調流程] > [活動] > [Apache Spark 應用程式]。 現在，您可以查看工作區中正在執行或已執行的所有 Spark 應用程式。
1. 尋找不再執行的應用程式，然後按一下其 [應用程式名稱]。 現在您可以看到 Spark 應用程式的詳細資料。
1. 如果您熟悉 Apache Spark，則可以按一下 [Spark 歷程記錄伺服器] 來找到標準的 Apache Spark 歷程記錄伺服器 UI。

## <a name="sql-activities"></a>SQL 活動

1. 瀏覽至 [協調流程] > [活動] > [SQL 要求]。
1. 在此檢視中，您可以看到 SQL 要求。
1. 選取要監視的 [集區]。 現在，您可以看到在該集區的工作區中正在執行或已執行的所有 SQL 要求。
1. 尋找特定的 SQL 要求，並將滑鼠暫留在該項目上。 暫留之後，您會看到 [SQL 指令碼] 圖示隨即出現。
1. 按一下 [SQL 指令碼] 圖示，以查看 SQL 要求的完整文字。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索知識中心](get-started-knowledge-center.md)
