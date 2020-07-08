---
title: 在 Azure Data Factory 中將連結服務參數化
description: 了解如何在 Azure Data Factory 中將連結服務參數化，並在執行階段傳遞動態值。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 85689661e7f0d170cd88edde8985f46285e679c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987788"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>在 Azure Data Factory 中將連結服務參數化
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

您現在可以將連結服務參數化，並在執行階段傳遞動態值。 例如，如果您想要連接到相同邏輯 SQL server 上的不同資料庫，您現在可以將連結服務定義中的資料庫名稱參數化。 這可讓您不需要為邏輯 SQL server 上的每個資料庫建立連結服務。 您也可以將連結服務中的其他屬性參數化，例如 *User name*。

您可以使用 Azure 入口網站或程式設計介面中的 Data Factory UI，將已連結的服務參數化。

> [!TIP]
> 建議您不要將密碼或秘密參數化。 請改為將所有連接字串儲存在 Azure Key Vault 中，並將 *Secret Name* 參數化。

如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>支援的資料存放區

目前，下列資料存放區的 Data Factory UI 中支援連結服務參數化。 至於其他資料存放區，您可以選取 [連線]**** 索引標籤上的 [程式碼]**** 圖示，並使用 JSON 編輯器，將連結的服務參數化。

- Amazon Redshift
- Azure Cosmos DB （SQL API）
- 適用於 MySQL 的 Azure 資料庫
- Azure SQL Database
- Azure Synapse Analytics (先前為 SQL DW)
- MySQL
- Oracle
- SQL Server

## <a name="data-factory-ui"></a>Data Factory UI

![將動態內容新增至連結服務定義](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![建立新的參數](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
