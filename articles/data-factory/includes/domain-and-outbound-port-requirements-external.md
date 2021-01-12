---
title: 包含檔案
description: 包含檔案
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 4ef7bc7ed1ab255bffdfec10c07bc040019d9f1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121581"
---
| 網域名稱                  | 輸出連接埠 | 描述                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | 當您使用分段複製功能時，可供自我裝載整合執行階段用來連線到 Azure 儲存體帳戶。 |
| `*.database.windows.net`      | 1433           | 只有當您在 Azure SQL Database 或 Azure Synapse Analytics (或選擇性的其他位置) 之間複製時才需要。 若要在不開啟連接埠 1433 的情況下，將資料複製到 SQL Database 或 Azure Synapse Analytics，請使用分段複製功能。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 只有當您在 Azure Data Lake Store 和選擇性的其他位置之間複製時才需要。 |
