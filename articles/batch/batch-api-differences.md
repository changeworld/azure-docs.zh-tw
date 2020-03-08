---
title: 管理 Api 和服務 Api 之間的差異-Azure Batch |Microsoft Docs
description: Api 適用于 Azure Batch 服務的不同層級。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672768"
---
# <a name="service-level-and-management-level-apis"></a>服務層級和管理層級 Api

Azure Batch 有兩組 Api，一個用於服務層級，另一個用於管理層級。 命名通常類似，但會傳回不同的結果。 如果您想要活動記錄，則必須使用管理 Api。 服務層級 Api 會略過 Azure 資源管理層，而不會記錄。


Batch 管理和 Batch 服務都有適用于集區的 Api，例如。 
- 要刪除集區的這個 API 會直接以 batch 帳戶為目標： https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- 這個用來刪除集區 https://docs.microsoft.com/rest/api/batchmanagement/pool/delete 的 API 是以 management.azure.com 層為目標。

