---
title: 媒體服務 v2 至 v3 遷移範例比較 |Microsoft Docs
description: 一組範例，可協助您比較 Azure 媒體服務 v2 與 v3 之間的程式碼差異。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 7705443bbe810866cd5b52946b310b5b79bfa072
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690287"
---
# <a name="media-services-migration-code-sample-comparison"></a>媒體服務遷移程式碼範例比較

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

您可以使用我們的一些程式碼範例，來比較 Sdk 之間的專案的執行方式。

## <a name="samples-for-comparison"></a>比較範例

下表列出在常見案例中，v2 和 v3 之間的比較範例。

|狀況|v2 API|v3 API|
|---|---|---|
|建立資產並上傳檔案 |[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|提交作業|[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>示範如何先建立 Transform，然後再提交 Job。|
|使用 AES 加密發行資產 |1. 建立 `ContentKeyAuthorizationPolicyOption`<br/>2. 建立 `ContentKeyAuthorizationPolicy`<br/>3. 建立 `AssetDeliveryPolicy`<br/>4. 建立 `Asset` 和上傳內容，或提交 `Job` 和使用 `OutputAsset`<br/>5. `AssetDeliveryPolicy` 與關聯 `Asset`<br/>6. 建立 `ContentKey`<br/>7. 附加 `ContentKey` 至 `Asset`<br/>8. 建立 `AccessPolicy`<br/>9. 建立 `Locator`<br/><br/>[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. 建立 `ContentKeyPolicy`<br/>2. 建立 `Asset`<br/>3. 上傳內容或使用 `Asset` as `JobOutput`<br/>4. 建立 `StreamingLocator`<br/><br/>[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|取得作業詳細資料及管理工作 |[使用 v2 管理作業](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[使用 v3 管理作業](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
