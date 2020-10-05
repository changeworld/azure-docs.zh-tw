---
title: 擷取程式碼中的共用存取簽章權杖 | Azure Key Vault
description: 受控儲存體帳戶功能可在 Azure Key Vault 與 Azure 儲存體帳戶之間提供完美整合。 此範例會使用 Azure SDK for .NET 來管理 SAS 權杖。
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 6072c71fa675bd203e94f3f42814a1183b12ae8e
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597979"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>建立 SAS 定義並擷取程式碼中的共用存取簽章權杖

您可以使用儲存在金鑰保存庫中的共用存取簽章 (SAS) 權杖來管理儲存體帳戶。 如需詳細資訊，請參閱[使用 SAS 授與 Azure 儲存體資源的存取權](../../storage/common/storage-sas-overview.md)。

> [!NOTE]
> 我們建議使用[角色型存取控制 (RBAC)](../../storage/common/storage-auth-aad.md) 來保護您的儲存體帳戶，以取得高級的安全性，並且讓透過「共用金鑰」授權使用更加方便。

本文提供 .NET 程式碼的範例，該程式碼會建立 SAS 定義及擷取 SAS 權杖。 如需完整詳細資料，包括為 Key Vault 受控儲存體帳戶產生的用戶端，請參閱我們的 [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) 範例。 如需如何建立及儲存 SAS 權杖的相關資訊，請參閱[使用 Key Vault 和 Azure CLI 管理儲存體帳戶金鑰](overview-storage-keys.md)，或[使用 Key Vault 和 Azure PowerShell 管理儲存體帳戶金鑰](overview-storage-keys-powershell.md)。

## <a name="code-samples"></a>程式碼範例

在下列範例中，我們將會建立 SAS 範本：

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

使用此範本，我們可以建立 SAS 定義 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

一旦建立 SAS 定義，您就可以使用 `SecretClient` 來擷取像是祕密的 SAS 權杖。 您需要在密碼名稱前面加上一個儲存體帳戶名稱，後面加上破折號：

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

如果您共用的存取簽章權杖即將到期，您可以再次擷取相同的祕密來產生新的權杖。

如需如何使用從 Key Vault SAS 權杖擷取來存取 Azure 儲存體服務的指南，請參閱[使用帳戶 SAS 存取 Blob 服務](https://docs.microsoft.com/azure/storage/common/storage-account-sas-create-dotnet#use-an-account-sas-from-a-client)

> [!NOTE]
> 如果您的應用程式從儲存體取得 403，就必須準備好重新整理 SAS，讓您可以處理金鑰遭到入侵的情況，而且您需要以比一般輪替週期更快的速度來進行輪替。 

## <a name="next-steps"></a>後續步驟
- 了解如何[使用 SAS 授與 Azure 儲存體資源的存取權](../../storage/common/storage-sas-overview.md)。
- 瞭解如何[使用 Key Vault 和 Azure CLI 或 [Azure PowerShell](overview-storage-keys-powershell.md) 來管理儲存體帳戶金鑰](overview-storage-keys.md)。
- 請參閱[受控儲存體帳戶金鑰範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
