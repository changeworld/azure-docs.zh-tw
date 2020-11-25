---
title: 包含檔案
description: 包含檔案
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005620"
---
## <a name="message-headers"></a>郵件標題
這些是您在訊息標頭中收到的屬性：

| 屬性名稱 | 描述 |
| ------------- | ----------- | 
| aeg-subscription-name | 事件訂閱的名稱。 |
| aeg-delivery-count | 對事件進行的嘗試次數。 |
| aeg-event-type | <p>事件類型。</p><p>它可能是下列其中一個值：</p><ul><li>SubscriptionValidation</li><li>通知</li><li>SubscriptionDeletion</li></ul> | 
| aeg-metadata-version | <p>事件的中繼資料版本。<p> 針對 **事件方格事件結構描述**，此屬性代表中繼資料版本；而針對 **雲端事件結構描述**，其代表 **規格版本**。 </p>|
| aeg-data-version | <p>事件的資料版本。</p><p>針對 **事件方格事件結構描述**，此屬性代表資料版本；而針對 **雲端事件結構描述**，則不會套用。</p> |
| aeg-output-event-id | 事件方格事件的識別碼。 |


