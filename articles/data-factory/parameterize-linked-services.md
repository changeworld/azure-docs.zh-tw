---
title: 在 Azure Data Factory 中將連結服務參數化
description: 了解如何在 Azure Data Factory 中將連結服務參數化，並在執行階段傳遞動態值。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: acc7284eb607d20ca1d62b478d802be56048bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440102"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>在 Azure Data Factory 中將連結服務參數化

您現在可以將連結服務參數化，並在執行階段傳遞動態值。 舉例來說，若要連線至同一部 Azure SQL Database 伺服器上的不同資料庫，您現在可以將連結服務定義中的資料庫名稱參數化。 這讓您無需為 Azure SQL 資料庫伺服器上的每個資料庫各建立一個連結服務。 您也可以將連結服務中的其他屬性參數化，例如 *User name*。

您可以在 Azure 門戶中使用資料工廠 UI 或程式設計介面對連結的服務進行參數化。

> [!TIP]
> 建議您不要將密碼或秘密參數化。 請改為將所有連接字串儲存在 Azure Key Vault 中，並將 *Secret Name* 參數化。

如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>支援的資料存放區

目前，Azure 入口網站的 Data Factory UI 中支援下列資料存放區的連結服務參數化。 至於其他資料存放區，您可以選取 [連線]**** 索引標籤上的 [程式碼]**** 圖示，並使用 JSON 編輯器，將連結的服務參數化。
- Azure SQL Database
- Azure SQL 資料倉儲
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- 適用於 MySQL 的 Azure 資料庫

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
