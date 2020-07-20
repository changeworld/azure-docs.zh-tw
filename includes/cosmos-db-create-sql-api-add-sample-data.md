---
title: 包含檔案
description: 包含檔案
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: f80efbac256871af073354f23317c447d6a85f1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "70020155"
---
您現在可以使用 [資料總管] 將資料新增至您的新容器。

1. 在 [資料總管]  中，展開 **Tasks** 資料庫，然後展開 **Items** 容器。 選取 [項目]  ，然後選取 [新增項目]  。

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. 現在將文件新增至具有下列結構的容器。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. 將 json 新增至 [文件]  索引標籤後，選取 [儲存]  。

    ![將 json 資料複製在 Azure 入口網站的 [資料總管] 中並選取 [儲存]](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  多建立並儲存一份文件，以便在其中插入 `id` 屬性的唯一值，並適當變更其他屬性。 新文件可擁有您想要的任何結構，因為 Azure Cosmos DB 不會對您的資料強加任何結構描述。