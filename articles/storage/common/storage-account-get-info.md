---
title: 使用 .NET 取得儲存體帳戶類型和 SKU 名稱
titleSuffix: Azure Storage
description: 瞭解如何使用 .NET 用戶端程式庫來取得 Azure 儲存體帳戶類型和 SKU 名稱。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 0a8eca9e7b3e890b67daf915ffe733dd54ef5896
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515054"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>使用 .NET 取得儲存體帳戶類型和 SKU 名稱

本文說明如何使用[適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)，取得 blob 的 Azure 儲存體帳戶類型和 SKU 名稱。

從2018-03-28 版開始，服務版本提供帳戶資訊。

## <a name="about-account-type-and-sku-name"></a>關於帳戶類型和 SKU 名稱

**帳戶類型**：有效的帳戶類型包括 `BlobStorage` 、 `BlockBlobStorage` 、 `FileStorage` 、 `Storage` 和 `StorageV2` 。 [Azure 儲存體帳戶總覽](storage-account-overview.md)包含詳細資訊，包括各種儲存體帳戶的描述。

**Sku 名稱**：有效的 sku 名稱包括 `Premium_LRS` 、 `Premium_ZRS` 、 `Standard_GRS` 、 `Standard_GZRS` 、、、 `Standard_LRS` `Standard_RAGRS` `Standard_RAGZRS` 和 `Standard_ZRS` 。 SKU 名稱會區分大小寫，而且在[SkuName 類別](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)中是字串欄位。

## <a name="retrieve-account-information"></a>取出帳戶資訊

若要取得與 blob 相關聯的儲存體帳戶類型和 SKU 名稱，請呼叫[GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet)或[GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet)方法。

下列程式碼範例會抓取並顯示唯讀帳戶屬性。

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>後續步驟

深入瞭解您可以透過[Azure 入口網站](https://portal.azure.com)和 Azure REST API 在儲存體帳戶上執行的其他作業。

- [取得帳戶資訊作業（REST）](/rest/api/storageservices/get-account-information)
