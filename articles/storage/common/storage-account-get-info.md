---
title: 獲取具有 .NET 的存儲帳戶類型和 SKU 名稱
titleSuffix: Azure Storage
description: 瞭解如何使用 .NET 用戶端庫獲取 Azure 存儲帳戶類型和 SKU 名稱。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137053"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>獲取具有 .NET 的存儲帳戶類型和 SKU 名稱

本文演示如何使用 .NET 的[Azure 存儲用戶端庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)獲取 Blob 的 Azure 存儲帳戶類型和 SKU 名稱。

帳戶資訊在服務版本 2018-03-28 開頭。

## <a name="about-account-type-and-sku-name"></a>關於帳戶類型和 SKU 名稱

**帳戶類型**：有效帳戶類型包括`BlobStorage` `BlockBlobStorage` `FileStorage`、、`Storage`和`StorageV2`。 [Azure 存儲帳戶概述](storage-account-overview.md)包含詳細資訊，包括各種存儲帳戶的說明。

**SKU 名稱**：有效的 SKU `Premium_LRS` `Premium_ZRS`名稱`Standard_GRS`包括`Standard_GZRS` `Standard_LRS`、、、、、、、、、、`Standard_RAGRS``Standard_RAGZRS`和`Standard_ZRS`。 SKU 名稱區分大小寫，是[SkuName 類](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)中的字串欄位。

## <a name="retrieve-account-information"></a>檢索帳戶資訊

要獲取與 Blob 關聯的存儲帳戶類型和 SKU 名稱，請調用[GetAccount 屬性](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet)或[GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet)方法。

以下代碼示例檢索並顯示唯讀帳戶屬性。

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

瞭解可以通過[Azure 門戶](https://portal.azure.com)和 Azure REST API 對存儲帳戶執行的其他操作。

- [獲取帳戶資訊操作 （REST）](/rest/api/storageservices/get-account-information)
