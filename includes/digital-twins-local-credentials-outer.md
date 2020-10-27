---
author: baanders
description: 包含用來在 Azure Digital Twins 範例中為 DefaultAzureCredential 設定本機驗證的檔案 (包含簡介)
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 56c63ee13c5e42350a2b544074a0b0527377ca4c
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494684"
---
### <a name="set-up-local-azure-credentials"></a>設定本機 Azure 認證

這個範例會使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (屬於 `Azure.Identity` 程式庫)，在本機電腦上執行時，使用 Azure Digital Twins 執行個體來驗證使用者。 如需用戶端應用程式可以使用 Azure Digital Twins 進行驗證的不同方式詳細資訊，請參閱 [*操作指南：撰寫應用程式驗證碼*](../articles/digital-twins/how-to-authenticate-client.md)。

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]