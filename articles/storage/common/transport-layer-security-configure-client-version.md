---
title: 為用戶端應用程式設定 (TLS) 的傳輸層安全性
titleSuffix: Azure Storage
description: 設定用戶端應用程式，以使用最低版本的傳輸層安全性 (TLS) 來與 Azure 儲存體進行通訊。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: d35b02e798d68682a3c55068f1f53f7a28d81721
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209387"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>為用戶端應用程式設定 (TLS) 的傳輸層安全性

基於安全考慮，Azure 儲存體帳戶可能會要求用戶端使用最低版本的傳輸層安全性 (TLS) 來傳送要求。 如果用戶端使用的 TLS 版本低於所需的最低版本，Azure 儲存體的呼叫將會失敗。 例如，如果儲存體帳戶需要 TLS 1.2，則使用 TLS 1.1 的用戶端所傳送的要求將會失敗。

本文說明如何設定用戶端應用程式，以使用特定的 TLS 版本。 如需有關如何為 Azure 儲存體帳戶設定 TLS 的最低必要版本的詳細資訊，請參閱[為儲存體帳戶設定傳輸層安全性 (tls) 的最低必要版本](transport-layer-security-configure-minimum-version.md)。

## <a name="configure-the-client-tls-version"></a>設定用戶端 TLS 版本

為了讓用戶端傳送具有特定 TLS 版本的要求，作業系統必須支援該版本。

下列範例示範如何從 PowerShell 或 .NET 將用戶端的 TLS 版本設定為1.2。 用戶端所使用的 .NET Framework 必須支援 TLS 1.2。 如需詳細資訊，請參閱[TLS 1.2 的支援](/dotnet/framework/network-programming/tls#support-for-tls-12)。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

下列範例顯示如何在 PowerShell 用戶端中啟用 TLS 1.2：

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

下列範例示範如何使用版本12的 Azure 儲存體用戶端程式庫，在 .NET 用戶端中啟用 TLS 1.2：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

下列範例示範如何使用版本11的 Azure 儲存體用戶端程式庫，在 .NET 用戶端中啟用 TLS 1.2：

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>確認用戶端所使用的 TLS 版本

若要確認用戶端已使用指定的 TLS 版本來傳送要求，您可以使用[Fiddler](https://www.telerik.com/fiddler)或類似的工具。 開啟 Fiddler 以開始捕獲用戶端網路流量，然後執行上一節中的其中一個範例。 查看 Fiddler 追蹤，以確認已使用正確的 TLS 版本來傳送要求，如下圖所示。

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="顯示 Fiddler 追蹤的螢幕擷取畫面，指出要求所使用的 TLS 版本":::

## <a name="next-steps"></a>後續步驟

- [為儲存體帳戶設定傳輸層安全性 (TLS) 的最低必要版本](transport-layer-security-configure-minimum-version.md)
- [Blob 儲存體的安全性建議](../blobs/security-recommendations.md)
