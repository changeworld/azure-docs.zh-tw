---
title: Azure Batch 集區刪除開始事件
description: Batch 集區刪除開始事件的參考。 集區刪除作業開始時，就會發出此事件。
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803743"
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
|`id`|String|集區識別碼。|
