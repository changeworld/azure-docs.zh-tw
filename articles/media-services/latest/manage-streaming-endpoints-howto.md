---
title: 使用 Azure 媒體服務 v3 管理串流端點
description: 本文示範如何使用 Azure 媒體服務 v3 來管理串流端點。
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c88a1f36d4e3405b44f36864262b2ab97517933
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500550"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>使用媒體服務 v3 管理串流端點

建立媒體服務帳戶時，**預設**[串流端點](streaming-endpoint-concept.md)會新增至您的帳戶處於 [**已停止**] 狀態。 若要開始串流處理您的內容，並利用[動態封裝](dynamic-packaging-overview.md)和[動態加密](content-protection-overview.md)功能，您想要串流內容的串流端點必須**處於 [執行**中] 狀態。

本文說明如何使用不同的技術，在您的串流端點上執行 [[啟動](/rest/api/media/streamingendpoints/start)] 命令。 
 
> [!NOTE]
> 只有當串流端點處於執行中狀態時，才會向您收取費用。
    
## <a name="prerequisites"></a>Prerequisites

檢閱： 

* [媒體服務概念](concepts-overview.md)
* [串流端點概念](streaming-endpoint-concept.md)
* [動態封裝](dynamic-packaging-overview.md)

## <a name="use-rest"></a>使用 REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

如需詳細資訊，請參閱 

* [開始 StreamingEndpoint](/rest/api/media/streamingendpoints/start)參考檔。
* 啟動串流端點是非同步作業。 

    如需有關如何監視長時間執行作業的詳細資訊，請參閱[長時間執行的作業](media-services-apis-overview.md)。
* 此[Postman 集合](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json)包含多個 REST 作業的範例，包括如何啟動串流端點。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站 
 
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 移至您的 Azure 媒體服務帳戶。
1. 在左窗格中，選取 [**串流端點**]。
1. 選取您想要啟動的串流端點，然後選取 [**啟動**]。

## <a name="use-the-azure-cli"></a>使用 Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

如需詳細資訊，請參閱[az ams 串流-端點啟動](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start)。

## <a name="use-sdks"></a>使用 SDK

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

請參閱完整的[JAVA 程式碼範例](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)。

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

請參閱完整的[.net 程式碼範例](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112)。

---

## <a name="next-steps"></a>後續步驟

* [媒體服務 v3 OpenAPI 規格（Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [串流端點作業](/rest/api/media/streamingendpoints)
