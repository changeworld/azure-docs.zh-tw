---
title: 快速入門：開始 - 建立 Synapse 工作區
description: 在本教學課程中，您將了解如何建立 Synapse 工作區、專用 SQL 集區和無伺服器 Apache Spark 集區。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 3a2636ec73d20f3011d8413c794e68ef41b1829c
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209180"
---
# <a name="creating-a-synapse-workspace"></a>建立 Synapse 工作區

在本教學課程中，您將了解如何建立 Synapse 工作區、專用 SQL 集區和無伺服器 Apache Spark 集區。 

## <a name="prerequisites"></a>必要條件

若要完成本教學課程的步驟，您必須能夠存取您已被指派其 **擁有者** 角色的資源群組。 在此資源群組中建立 Synapse 工作區。

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>在 Azure 入口網站中建立 Synapse 工作區

1. 開啟 [Azure 入口網站](https://portal.azure.com)，並在頂端搜尋 **Synapse**。
1. 在 [服務] 底下的搜尋結果中，選取 [Azure Synapse Analytics]。
1. 選取 [新增] 以建立工作區。
1. 在 [ **基本** ] 索引標籤的 [專案詳細資料] 底下，輸入您慣用的 **訂** 用帳戶、 **資源群組**、 **區域**，然後選擇工作區名稱。 在本教學課程中，我們將使用 **myworkspace**。
1. **選取 Data Lake Storage Gen 2**]，然後按一下 [**從訂用** 帳戶] 的按鈕。
1. 在 [ **帳戶名稱**] 中，按一下 [ **建立新** 的]，並為新的儲存體帳戶 **contosolake** 命名，或類似此名稱必須是唯一的。
1. 依 **檔案系統名稱**，按一下 [ **建立新** 的]，並為 it **使用者** 命名。 這會建立名為 **users** 的儲存體容器
1. 工作區會使用此儲存體帳戶作為 Spark 資料表和 Spark 應用程式記錄的「主要」儲存體帳戶。
1. 核取 [將您的儲存體 Blob 資料參與者角色指派給 Data Lake Storage Gen2 帳戶] 方塊。 
1. 選取 [檢閱+建立] > [建立]。 您的工作區將會在幾分鐘內就緒。

> [!NOTE]
> 若要從現有的專用 SQL 集區 (先前稱為 SQL DW) 啟用工作區功能，請參閱[如何為您的專用 SQL 集區 (先前稱為 SQL DW) 啟用工作區](./sql-data-warehouse/workspace-connected-create.md)。


## <a name="open-synapse-studio"></a>開啟 Synapse Studio

建立 Azure Synapse 工作區之後，有兩種方式可以開啟 Synapse Studio：

* 在 [Azure 入口網站](https://portal.azure.com)中開啟您的 Synapse 工作區，在 [Synapse] 工作區的 [ **總覽** ] 區段中，選取 [開啟 Synapse Studio] 方塊中的 [ **開啟** ]。
* 移至 `https://web.azuresynapse.net` 並登入您的工作區。


## <a name="the-built-in-serverless-sql-pool"></a>內建的無伺服器 SQL 集區

每個工作區都有一個預先建置的無伺服器 SQL 集區，稱為 **內建**。 您無法刪除此集區。 無伺服器 SQL 集區可讓您使用 SQL，而無須保留專用 SQL 集區的容量。 不同於專用 SQL 集區，無伺服器 SQL 集區的計費是根據掃描以執行查詢的資料量，而不是配置給集區的容量數目。


## <a name="create-a-dedicated-sql-pool"></a>建立專用 SQL 集區

1. 在 Synapse Studio 的左側窗格中，選取 [管理] > [SQL 集區]。
1. 選取 [新增]
1. 針對 [SQL 集區名稱]，選取 **SQLPOOL1**
1. 針對 [效能層級]，選擇 **DW100C**
1. 選取 [檢閱+建立] > [建立]。 您的專用 SQL 集區將會在幾分鐘內就緒。 您的專用 SQL 集區會與專用 SQL 集區資料庫 (也稱為 **SQLPOOL1**) 相關聯。

專用 SQL 集區只要在作用中，就會取用計費的資源。 您可於稍後暫停集區，以降低成本。

> [!NOTE] 
> 在您的工作區中建立新的專用 SQL 集區 (先前稱為 SQL DW) 時，將會開啟專用 SQL 集區佈建頁面。 佈建會在邏輯 SQL 伺服器上進行。


## <a name="create-a-serverless-apache-spark-pool"></a>建立無伺服器 Apache Spark 集區

1. 在 Synapse Studio 的左側窗格中，選取 [管理] > [Apache Spark 集區]。
1. 選取 [新增] 
1. 針對 [Apache Spark 集區名稱]，輸入 **Spark1**。
1. 針對 [節點大小]，輸入 **Small**。
1. 針對 [節點數目]，將最小值和最大值都設定為 3
1. 選取 [檢閱+建立] > [建立]。 您的 Apache Spark 集區會在幾秒內準備就緒。

Spark 集區會通知 Azure Synapse 要使用多少 Spark 資源。 您只需就您使用的資源付費。 當您主動停止使用集區時，資源將會自動逾時並予以回收。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用專用 SQL 集區進行分析](get-started-analyze-sql-pool.md)
