---
title: 在 Azure Data Factory 中將連結服務參數化
description: 了解如何在 Azure Data Factory 中將連結服務參數化，並在執行階段傳遞動態值。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2021
author: dcstwh
ms.author: weetok
manager: anandsub
ms.openlocfilehash: e463328df195b5a91db8ce272d138f980d000e79
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232058"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>在 Azure Data Factory 中將連結服務參數化

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

您現在可以將連結服務參數化，並在執行階段傳遞動態值。 例如，如果您想要連接到相同邏輯 SQL server 上的不同資料庫，您現在可以將連結服務定義中的資料庫名稱參數化。 這可防止您必須針對邏輯 SQL server 上的每個資料庫建立連結服務。 您也可以將連結服務中的其他屬性參數化，例如 *User name*。

您可以使用 Azure 入口網站中的 Data Factory UI 或程式設計介面，將連結的服務參數化。

> [!TIP]
> 建議您不要將密碼或秘密參數化。 請改為將所有連接字串儲存在 Azure Key Vault 中，並將 *Secret Name* 參數化。

如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>支援的連結服務類型

您可以將任何類型的連結服務參數化。
在 UI 上撰寫連結服務時，Data Factory 會針對下列類型的連結服務提供內建的參數化體驗。 在 [連結服務建立/編輯] 分頁中，您可以找到新參數的選項，並新增動態內容。

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (SQL API) 
- 適用於 MySQL 的 Azure 資料庫
- Azure Databricks
- Azure Key Vault
- Azure SQL Database
- Azure SQL 受控執行個體
- Azure Synapse Analytics 
- MySQL
- Oracle
- SQL Server
- 一般 HTTP
- 一般 REST

針對不在上述清單中的其他連結服務類型，您可以在 UI 上編輯 JSON，以將連結的服務參數化：

- 在 [已連結的服務建立/編輯] 分頁-> 展開底部 > 核取 [以 JSON 格式指定動態內容] 核取方塊-> 指定連結服務 JSON 承載。 
- 或者，在您建立未參數化的連結服務之後，請在 [ [管理中樞](author-visually.md#management-hub) -> 連結的服務] 中，> 尋找特定的連結服務-> 按一下 [程式碼] (按鈕 " {} " ) 編輯 JSON。 

請參閱 [JSON 範例](#json) 以加入 ` parameters` 區段，以定義參數並使用參考參數 ` @{linkedService().paraName} ` 。

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
