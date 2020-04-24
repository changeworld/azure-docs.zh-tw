---
title: 管理 Api 和服務 Api 之間的差異
description: Api 適用于 Azure Batch 服務的不同層級。
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115392"
---
# <a name="service-level-and-management-level-apis"></a>服務層級和管理層級 Api

Azure Batch 有兩組 Api，一個用於服務層級，另一個用於管理層級。 命名通常類似，但會傳回不同的結果。 如果您想要活動記錄，則必須使用管理 Api。 服務層級 Api 會略過 Azure 資源管理層，而不會記錄。


Batch 管理和 Batch 服務都有適用于集區的 Api，例如。 
- 此要刪除集區的 API 是直接針對 batch 帳戶：https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- 此 API 刪除集https://docs.microsoft.com/rest/api/batchmanagement/pool/delete區的目標是 management.azure.com 層。

