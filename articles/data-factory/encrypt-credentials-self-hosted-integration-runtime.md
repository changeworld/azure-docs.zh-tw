---
title: 加密 Azure Data Factory 中的認證
description: 了解如何在含自我裝載整合執行階段的電腦上加密和儲存內部部署資料存放區的認證。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: cd775c5a3bf367600a4537a9409a9bb8f902f588
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82628975"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>在 Azure Data Factory 中加密內部部署資料存放區的認證

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

您可以在含自我裝載整合執行階段的電腦上加密和儲存內部部署資料存放區的認證 (含敏感性資訊的連結服務)。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您會將具有認證的 JSON 定義檔傳遞至 <br/>[**AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) Cmdlet 可產生具有加密認證的輸出 JSON 定義檔案。 然後，使用更新的 JSON 定義來建立連結服務。

## <a name="author-sql-server-linked-service"></a>編寫 SQL Server 連結服務
在任何資料夾中，使用下列內容建立名為 **SqlServerLinkedService.json** 的 JSON 檔案：  

先將 `<servername>`、`<databasename>`、`<username>` 和 `<password>` 取代為 SQL Server 的值，再儲存檔案。 並且，將 `<integration runtime name>` 取代為您整合執行階段的名稱。 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>加密認證
若要從內部部署自我裝載整合執行時間的 JSON 承載加密機密資料，請執行**AzDataFactoryV2LinkedServiceEncryptedCredential**，並傳遞 json 承載。 這個 Cmdlet 確保使用 DPAPI 來加密認證，並將其本機儲存在自我裝載整合執行階段節點上。 包含認證之加密參考的輸出裝載可以重新導向至另一個 JSON 檔案（在此案例中為 ' encryptedLinkedService.json '）。

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>搭配使用 JSON 與加密認證
現在，使用包含加密認證之前一個命令的輸出 JSON 檔案來設定 **SqlServerLinkedService**。

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>後續步驟
如需資料移動安全性考量的資訊，請參閱[資料移動安全性考量](data-movement-security-considerations.md)。

