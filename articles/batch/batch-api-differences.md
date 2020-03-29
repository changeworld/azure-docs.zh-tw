---
title: 管理 API 和服務 API 之間的差異 - Azure 批次處理 |微軟文檔
description: API 在 Azure 批次處理服務的不同層上工作。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672768"
---
# <a name="service-level-and-management-level-apis"></a>服務等級和服務等級 API

Azure Batch 有兩組 API，一組用於服務等級，另一組用於管理級別。 命名通常相似，但它們返回的結果不同。 如果需要活動日誌，則需要使用管理 API。 服務等級 API 繞過 Azure 資源管理層，並且未記錄。


例如，批次處理管理和批次處理服務都具有池 API。 
- 此要刪除池的 API 直接針對批次處理帳戶：https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- 此要刪除池https://docs.microsoft.com/rest/api/batchmanagement/pool/delete的 API 面向management.azure.com層。

