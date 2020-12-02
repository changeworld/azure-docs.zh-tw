---
title: 包含檔案
description: 包含檔案
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 626074940ced57bdb1ae93f494b7a3847ef83a81
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445015"
---
| 網域名稱                  | 輸出連接埠 | 描述                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | 必須提供此資訊，自我裝載整合執行階段才能進行互動式撰寫。 |
| `{datafactory}.{region}.datafactory.azure.net`<br> 或 `*.frontend.clouddatahub.net` | 443            | 必須提供此資訊，自我裝載整合執行階段才能連線到 Data Factory 服務。 <br>針對新建立的 Data Factory，請從您的自我裝載整合執行階段金鑰 (格式為 {datafactory}.{region}.datafactory.azure.net) 中尋找 FQDN。 針對舊版 Data Factory，如果您在自我裝載整合金鑰中看不到 FQDN，請改為使用 *.frontend.clouddatahub.net。 |
| `download.microsoft.com`    | 443            | 自我裝載整合執行階段所需，以用於下載更新。 如果您已停用自動更新，您可以略過設定此網域。 |
| `*.core.windows.net`          | 443            | 當您使用[分段複製](../articles/data-factory/copy-activity-performance.md#staged-copy)功能時，可供自我裝載整合執行階段用來連線到 Azure 儲存體帳戶。 |
| `*.database.windows.net`      | 1433           | 只有當您在 Azure SQL Database 或 Azure Synapse Analytics (或選擇性的其他位置) 之間複製時才需要。 若要在不開啟連接埠 1433 的情況下，將資料複製到 SQL Database 或 Azure Synapse Analytics，請使用分段複製功能。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 只有當您在 Azure Data Lake Store 和選擇性的其他位置之間複製時才需要。 |
