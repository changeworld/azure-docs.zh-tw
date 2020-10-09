---
title: 不同 Azure 儲存體位置的範例資料-Team Data 科學流程
description: 在 Azure Blob 容器、SQL Server 和 Hive 資料表上進行資料取樣，使其成為更小但具代表性且更容易管理的大小。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76718598"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Azure Blob 容器、SQL Server 和 Hive 資料表中的範例資料

下列文章說明如何對三個不同 Azure 位置中任一個位置所儲存的資料進行取樣：

* [**Azure blob 容器資料**](sample-data-blob.md) 的取樣方式是以程式設計方式下載，然後使用範例 Python 程式碼來進行取樣。
* [**SQL Server 資料**](sample-data-sql-server.md)會使用 SQL 和 Python 程式設計語言進行取樣。 
* [**Hive 資料表**](sample-data-hive.md)會使用 Hive 查詢進行取樣。

這個取樣工作是 [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)中的一個步驟。

**為何要對資料進行取樣？**

如果您規劃分析的資料集很龐大，通常最好是對資料進行向下取樣，將資料縮減為更小但具代表性且更容易管理的大小。 縮減可能有助於資料理解、探索和特徵工程設計。 此「Cortana 分析」程式中的「取樣」角色，是為了能夠快速建立資料處理函式與機器學習模型的原型。

