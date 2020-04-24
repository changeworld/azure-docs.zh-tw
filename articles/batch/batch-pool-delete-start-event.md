---
title: Azure Batch 集區刪除開始事件
description: Batch 集區刪除開始事件的參考。 集區刪除作業開始時，就會發出此事件。
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 38e419e549006d3fde2f1694e0d40e620cd438e5
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115919"
---
# <a name="pool-delete-start-event"></a>集區刪除開始事件

 集區刪除作業開始時，就會發出此事件。 由於集區刪除為非同步事件，因此您可以預期當刪除作業完成時，就會發出集區刪除完成事件。

 下列範例顯示集區刪除開始事件內文。

```
{
    "id": "myPool1"
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|`id`|String|集區的識別碼。|
