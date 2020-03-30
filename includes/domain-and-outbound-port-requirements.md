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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559313"
---
| 網域名稱                  | 輸出連接埠 | 描述                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | 自託管的集成運行時需要連接到 Azure 資料工廠中的資料移動服務。 |
| `*.frontend.clouddatahub.net` | 443            | 必須提供此資訊，自我裝載整合執行階段才能連線到 Data Factory 服務。 |
| `download.microsoft.com`    | 443            | 自我裝載整合執行階段所需，以用於下載更新。 如果禁用了自動更新，可以跳過配置此域。 |
| `*.core.windows.net`          | 443            | 當您使用[分段複製](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)功能時，可供自我裝載整合執行階段用來連線到 Azure 儲存體帳戶。 |
| `*.database.windows.net`      | 1433           | 僅當從 Azure SQL 資料庫或 Azure SQL 資料倉儲複製時才需要，否則即可進行可選操作。 使用暫存複製功能將資料複製到 SQL 資料庫或 SQL 資料倉儲，而無需打開端口 1433。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 僅在從 Azure 資料湖存儲複製時才需要，否則即可進行可選。 |
