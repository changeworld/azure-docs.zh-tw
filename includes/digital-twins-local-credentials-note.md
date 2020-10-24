---
author: baanders
description: 在 Azure 數位 Twins 範例中包含 DefaultAzureCredential 的檔案-附注
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494478"
---
>[!NOTE]
> 此範例會使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet)) 的程式庫 (部分，以在您于 `Azure.Identity` 本機電腦上執行時使用 Azure 數位 Twins 實例來驗證使用者。 透過這種類型的驗證，此範例會在您的本機環境中搜尋 Azure 認證，例如本機 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 或 Visual Studio/Visual Studio Code 的登入。
>
> 如需使用 `DefaultAzureCredential` 和其他驗證選項的詳細資訊，請參閱作法 [*：撰寫應用程式驗證碼*](../articles/digital-twins/how-to-authenticate-client.md)。