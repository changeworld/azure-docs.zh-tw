---
title: 教學課程：開始建立 Synapse 工作區
description: 在本教學課程中，您將了解如何建立 Synapse 工作區、SQL 集區和 Apache Spark 集區。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 08/27/2020
ms.openlocfilehash: 56292d3e8ba4c9ec89d73f10640264c178f8a9a7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255013"
---
# <a name="create-a-synapse-workspace"></a>建立 Synapse 工作區

在本教學課程中，您將了解如何建立 Synapse 工作區、SQL 集區和 Apache Spark 集區。 

## <a name="create-a-synapse-workspace"></a>建立 Synapse 工作區

1. 開啟 [Azure 入口網站](https://portal.azure.com)，並在頂端搜尋 **Synapse**。
1. 在 [服務] 底下的搜尋結果中，選取 [Azure Synapse Analytics (工作區預覽)]。
1. 選取 [新增]，使用這些設定來建立工作區：

    |索引標籤|設定 | 建議的值 | 描述 |
    |---|---|---|---|
    |基本概念|**工作區名稱**|您可以賦予其任何名稱。| 在本文件中，我們將使用 **myworkspace**。|
    |基本概念|**區域**|比對儲存體帳戶的區域。|

1. 您需要 ADLSGEN2 帳戶來建立工作區。 最簡單的選擇是建立新的帳戶。 如果想要重複使用現有的帳戶，則必須執行一些額外的設定。 
1. 選項 1 建立新的 ADLSGEN2 帳戶 
    1. 在**選取 Data Lake Storage Gen 2** 中按一下 [建立新的] 並命名為 **contosolake**。
    1. 在**選取 Data Lake Storage Gen 2** 中按一下 [檔案系統] 並命名為 **users**。
1. 選項 2 請參閱本文件末尾的**準備儲存體帳戶**指示。
1. 您的 Azure Synapse 工作區會使用此儲存體帳戶作為「主要」儲存體帳戶，以及用來儲存工作區資料的容器。 工作區會將資料儲存在 Apache Spark 資料表中。 其會將 Spark 應用程式記錄儲存在名為 **/synapse/workspacename** 的資料夾底下。
1. 選取 [檢閱+建立] > [建立]。 您的工作區將會在幾分鐘內就緒。


## <a name="open-synapse-studio"></a>開啟 Synapse Studio

建立 Azure Synapse 工作區之後，有兩種方式可以開啟 Synapse Studio：

* 在 [Azure 入口網站](https://portal.azure.com)中開啟 Synapse 工作區。 在 [概觀] 區段的頂端，選取 [啟動 Synapse Studio]。
* 移至 `https://web.azuresynapse.net` 並登入您的工作區。

## <a name="create-a-sql-pool"></a>建立 SQL 集區

1. 在 Synapse Studio 的左側窗格中，選取 [管理] > [SQL 集區]。
1. 選取 [新增]，然後輸入下列設定：

    |設定 | 建議的值 | 
    |---|---|---|
    |**SQL 集區名稱**| **SQLDB1**|
    |**效能等級**|**DW100C**|
    |||

1. 選取 [檢閱+建立] > [建立]。 您的 SQL 集區將會在幾分鐘內就緒。 您的 SQL 集區會與 SQL 集區資料庫 (也稱為 **SQLDB1**) 相關聯。

SQL 集區只要在作用中，就會取用計費的資源。 您可於稍後暫停集區，以降低成本。

## <a name="create-an-apache-spark-pool"></a>建立 Apache Spark 集區

1. 在 Synapse Studio 的左側窗格中，選取 [管理] > [Apache Spark 集區]。
1. 選取 [新增]，然後輸入下列設定：

    |設定 | 建議的值 | 
    |---|---|---|
    |**Apache Spark 集區名稱**|**Spark1**
    |**節點大小**| **小型**|
    |**節點數目**| 將最小值設定為 3，以及最大值設定為 3|

1. 選取 [檢閱+建立] > [建立]。 您的 Apache Spark 集區會在幾秒內準備就緒。

> [!NOTE]
> 儘管名稱為 Apache Spark 集區和 SQL 集區，但這兩者並不相同。 這只是一些基本中繼資料，可讓您用來告知 Azure Synapse 工作區如何與 Spark 互動。

由於其為中繼資料，因此無法啟動或停止 Spark 集區。

當您在 Azure Synapse 中執行 Spark 活動時，需指定要使用的 Spark 集區。 集區會通知 Azure Synapse 要使用多少 Spark 資源。 您只需就您使用的資源付費。 當您主動停止使用集區時，資源將會自動逾時並予以回收。

> [!NOTE]
> 會從 Spark 集區獨立建立 Spark 資料庫。 工作區一律會有一個名為 **default** 的 Spark 資料庫。 您可以建立其他 Spark 資料庫。

## <a name="the-sql-on-demand-pool"></a>SQL 隨選集區

每個工作區都有一個預先建立的集區，稱為 **SQL 隨選**。 您無法刪除此集區。 SQL 隨選集區可讓您使用 SQL，而不需要在 Azure Synapse 中建立或考慮管理 SQL 集區。

不同於其他類型的集區，SQL 隨選計費是根據掃描以執行查詢的資料量，而不是用於執行查詢的資源數目。

* SQL 隨選有自己的 SQL 隨選資料庫，與任何 SQL 隨選集區分開存在。
* 工作區一律只會有一個名為 **SQL 隨選**的 SQL 隨選集區。

## <a name="prepare-a-storage-account"></a>準備儲存體帳戶

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
1. 建立具有下列設定的新儲存體帳戶：

    |索引標籤|設定 | 建議的值 | 描述 |
    |---|---|---|---|
    |基本概念|**儲存體帳戶名稱**| 選擇任何名稱。| 在本文件中，我們將使用 **contosolake** 這個名稱。|
    |基本概念|**帳戶類型**| **StorageV2** ||
    |基本概念|**位置**|選擇任何位置。| 我們建議您將 Azure Synapse Analytics 工作區和 Azure Data Lake Storage Gen2 帳戶置於相同區域。|
    |進階|**Data Lake Storage Gen2**|**已啟用**| Azure Synapse 僅適用於已啟用此設定的儲存體帳戶。|
    |||||

1. 建立儲存體帳戶之後，請選取左側窗格中的 [存取控制 (IAM)]。 然後指派下列角色，或確定其已指派：
    * 將您自己指派給**擁有者**角色。
    * 將您自己指派給**儲存體 Blob 資料擁有者**角色。
1. 在左側窗格中，選取 [容器] 並建立容器。
1. 您可以為容器提供任何名稱。 在本文件中，我們會將容器命名為 **users**。
1. 接受 [公用存取層級] 的預設設定，然後選取 [建立]。

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>從您的工作區設定儲存體帳戶的存取權

Azure Synapse 工作區的受控身分識別可能已經有儲存體帳戶的存取權。 請依照下列步驟來確認：

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後開啟為您工作區選擇的主要儲存體帳戶。
1. 從左窗格中選取 [存取控制 (IAM)]。
1. 指派下列角色，或確定其已指派。 我們使用相同名稱作為工作區身分識別和工作區名稱。
    * 針對儲存體帳戶上的 [儲存體 Blob 資料參與者] 角色，請指派 **myworkspace** 作為工作區身分識別。
    * 指派 **myworkspace** 作為工作區名稱。

1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 SQL 集區進行分析](get-started-analyze-sql-pool.md)
