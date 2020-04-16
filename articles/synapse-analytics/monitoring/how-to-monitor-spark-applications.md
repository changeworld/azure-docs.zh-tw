---
title: 監控阿帕契火花應用程式
description: 使用 Azure 同步工作室監視 Apache Spark 應用程式。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430743"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>使用 Azure 同步工作室(預覽)監視 Apache Spark 應用程式

使用 Azure 同步分析,可以使用 Spark 在工作區中的 Spark 池上運行筆記本、作業和其他類型的應用程式。

本文介紹如何監視 Spark 應用程式,使您能夠關注最新的狀態、問題和進度。

## <a name="accessing-the-list-of-spark-applications"></a>存取 Spark 應用程式清單

要查看工作區中的 Spark 應用程式清單,請首先[打開 Azure 同步工作室](https://web.azuresynapse.net/)並選擇工作區。

  > [!div class="mx-imgBorder"]
  > ![登入工作區](./media/common/login-workspace.png)

打開工作區后,選擇左側的 **「監視器」** 部分。

  > [!div class="mx-imgBorder"]
  > ![選擇監視器中心](./media/common/left-nav.png)

選擇**Spark 應用程式**以查看 Spark 應用程式的清單。

  > [!div class="mx-imgBorder"]
  > ![選擇 Spark 應用程式](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>篩選 Spark 應用程式

您可以將 Spark 應用程式清單篩選為您感興趣的應用程式。 螢幕頂部的篩選器允許您指定要篩選的欄位。

例如,您可以篩選檢視以檢視僅查看包含名稱為「銷售」的 Spark 應用程式:

  > [!div class="mx-imgBorder"]
  > ![[篩選] 按鈕](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![樣品過濾器](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>檢視有關特定 Spark 應用程式的詳細資訊

要查看有關其中一個 Spark 應用程式的詳細資訊,請選擇 Spark 應用程式並查看詳細資訊。 如果 Spark 應用程式仍在運行,則可以監視進度。

## <a name="next-steps"></a>後續步驟

有關監視管道運行的詳細資訊,請參閱[監視器管道運行 Azure Synapse Studio](how-to-monitor-pipeline-runs.md)一文。  
