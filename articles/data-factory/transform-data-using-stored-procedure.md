---
title: 使用預存程式活動來轉換資料
description: 說明如何使用 SQL Server 預存程序活動，以從 Data Factory 管線叫用 Azure SQL Database/資料倉儲中的預存程序。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: 8543276a338b523a290fb131a8f1b7a55affbd98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85248967"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>使用 Azure Data Factory 中的 SQL Server 預存程序活動轉換資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-stored-proc-activity.md)
> * [目前的版本](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

您在 Data Factory [管線](concepts-pipelines-activities.md)中使用資料轉換活動，以轉換和處理您的原始資料以進行預測和深入了解。 預存程序活動是 Data Factory 支援的其中一個轉換活動。 本文是以[轉換資料](transform-data.md)一文為基礎，提供 Data Factory 中資料轉換及所支援轉換活動的一般概觀。

> [!NOTE]
> 如果您是 Azure Data Factory 的新手，請在閱讀本文之前先閱讀 [Azure Data Factory 簡介](introduction.md)，以及研習[教學課程：轉換資料](tutorial-transform-data-spark-powershell.md)。 

您可以使用「預存程序活動」來叫用您企業或 Azure 虛擬機器 (VM) 中的下列其中一個資料存放區： 

- Azure SQL Database
- Azure Synapse Analytics (先前稱為 Azure SQL 資料倉儲)
- SQL Server Database。  如果您使用 SQL Server，請在裝載資料庫的同一部電腦上或可存取資料庫的個別電腦上安裝自我裝載整合執行階段。 自我裝載整合執行階段是一套透過安全且可管理的方式，將內部部署/Azure VM 上的資料來源連結至雲端服務的元件。 如需詳細資訊，請參閱[自我裝載整合運行](create-self-hosted-integration-runtime.md)時間一文。

> [!IMPORTANT]
> 將資料複製到 Azure SQL Database 或 SQL Server 時，您可以使用 **sqlWriterStoredProcedureName** 屬性在複製活動中設定 **SqlSink** 以叫用預存程序。 如需有關此屬性的詳細資料，請參閱下列連接器文章：[Azure SQL Database](connector-azure-sql-database.md)、[SQL Server](connector-sql-server.md)。 不支援使用複製活動將資料複製到 Azure Synapse 分析（先前為 Azure SQL 資料倉儲）來叫用預存程式。 但是，您可以使用預存程序活動來叫用「SQL 資料倉儲」中的預存程序。 
>
> 從 Azure SQL Database 或 SQL Server 或 Azure Synapse Analytics （先前為 Azure SQL 資料倉儲）複製資料時，您可以在複製活動中設定**SqlSource** ，以叫用預存程式，藉由使用**sqlReaderStoredProcedureName**屬性從源資料庫讀取資料。 如需詳細資訊，請參閱下列連接器文章： [Azure SQL Database](connector-azure-sql-database.md)、 [SQL Server](connector-sql-server.md)、 [Azure Synapse Analytics （先前為 Azure SQL 資料倉儲）](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>語法詳細資料
以下是 JSON 格式來定義預存程序活動︰

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

下表說明這些 JSON 屬性：

| 屬性                  | 說明                              | 必要 |
| ------------------------- | ---------------------------------------- | -------- |
| NAME                      | 活動的名稱                     | Yes      |
| description               | 說明活動用途的文字 | 否       |
| type                      | 對於預存程序活動，活動類型為 **SqlServerStoredProcedure** | 是      |
| linkedServiceName         | 參考**Azure SQL Database**或**Azure Synapse 分析（先前稱為 Azure SQL 資料倉儲）** 或**SQL Server**在 Data Factory 中註冊為連結服務。 若要深入了解此已連結的服務，請參閱[計算已連結的服務](compute-linked-services.md)一文。 | 是      |
| storedProcedureName       | 指定要叫用的預存程序名稱。 | Yes      |
| storedProcedureParameters | 指定預存程序參數的值。 使用 `"param1": { "value": "param1Value","type":"param1Type" }` 來傳遞資料來源所支援的參數值及其類型。 如果您需要為參數傳遞 Null，請使用 `"param1": { "value": null }` (全部小寫)。 | No       |

## <a name="parameter-data-type-mapping"></a>參數資料類型對應
您為參數指定的資料類型是對應至您所使用之資料來源中資料類型的 Azure Data Factory 類型。 您可以在 [連接器] 區域中找到資料來源的資料類型對應。 範例包括

| 資料來源          | 資料型別對應 |
| ---------------------|-------------------|
| Azure Synapse Analytics (先前稱為 Azure SQL 資料倉儲) | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>錯誤資訊

當預存程序失敗並傳回錯誤詳細資料時，您無法直接在活動輸出中擷取錯誤資訊。 不過，Data Factory 會將其所有活動執行事件提取至 Azure 監視器。 在 Data Factory 提取至 Azure 監視器的事件中，它會將錯誤詳細資料推送至該處。 例如，您可以從這些事件設定電子郵件警示。 如需詳細資訊，請參閱[使用 Azure 監視器提出警示及監視 Data Factory](monitor-using-azure-monitor.md)。

## <a name="next-steps"></a>後續步驟
請參閱下列文章，其說明如何以其他方式轉換資料： 

* [U-SQL 活動](transform-data-using-data-lake-analytics.md)
* [Hive 活動](transform-data-using-hadoop-hive.md)
* [Pig 活動](transform-data-using-hadoop-pig.md)
* [MapReduce 活動](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 串流活動](transform-data-using-hadoop-streaming.md)
* [Spark 活動](transform-data-using-spark.md)
* [.NET 自訂活動](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning 批次執行活動](transform-data-using-machine-learning.md)
* [預存程序活動](transform-data-using-stored-procedure.md)
