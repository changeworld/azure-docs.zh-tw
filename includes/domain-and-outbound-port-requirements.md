---
title: 包含檔案
description: 包含檔案
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74559313"
---
| 網域名稱                  | 輸出連接埠 | 說明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | 自我裝載整合執行時間所需，以便連接到 Azure Data Factory 中的資料移動服務。 |
| `*.frontend.clouddatahub.net` | 443            | 必須提供此資訊，自我裝載整合執行階段才能連線到 Data Factory 服務。 |
| `download.microsoft.com`    | 443            | 自我裝載整合執行階段所需，以用於下載更新。 如果您已停用自動更新，您可以略過設定此網域。 |
| `*.core.windows.net`          | 443            | 當您使用[分段複製](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)功能時，可供自我裝載整合執行階段用來連線到 Azure 儲存體帳戶。 |
| `*.database.windows.net`      | 1433           | 只有當您從或複製 Azure SQL Database 或 Azure SQL 資料倉儲和選擇性時才需要。 使用分段複製功能，將資料複製到 SQL Database 或 SQL 資料倉儲，而不會開啟埠1433。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 只有當您從或複製到 Azure Data Lake 存放區，或者是選擇性時，才需要。 |
