---
title: 使用 Azure 媒體服務 v3 管理流式處理終結點
description: 本文演示如何使用 Azure 媒體服務 v3 管理流式處理終結點。
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
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461039"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>使用媒體服務 v3 管理流式處理終結點

創建媒體服務帳戶時，**預設**[流式處理終結點](streaming-endpoint-concept.md)將添加到處于 **"已停止"** 狀態的帳戶。 要開始資料流內容並利用[動態打包](dynamic-packaging-overview.md)和[動態加密](content-protection-overview.md)，要從中資料流內容的流式處理終結點必須處於 **"正在運行"** 狀態。

本文介紹如何使用不同的技術在流式處理終結點上執行[Start](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)命令。 
 
> [!NOTE]
> 只有當串流端點處於執行中狀態時，才會向您收取費用。
    
## <a name="prerequisites"></a>Prerequisites

檢討： 

* [媒體服務概念](concepts-overview.md)
* [流式處理終結點概念](streaming-endpoint-concept.md)
* [動態封裝](dynamic-packaging-overview.md)

## <a name="use-rest"></a>使用 REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

如需詳細資訊，請參閱 

* [啟動流式處理終結點](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)參考文檔。
* 啟動流式處理終結點是非同步作業。 

    有關如何監視長時間運行的操作的資訊，請參閱[長時間運行的操作](media-services-apis-overview.md)。
* 此[Postman 集合](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json)包含多個 REST 操作的示例，包括有關如何啟動流式處理終結點的示例。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站 
 
1. 登錄到 Azure[門戶](https://portal.azure.com/)。
1. 轉到 Azure 媒體服務帳戶。
1. 在左側窗格中，選擇**流式處理終結點**。
1. 選擇要啟動的流式處理終結點，然後選擇 **"開始**"。

## <a name="use-the-azure-cli"></a>使用 Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

有關詳細資訊，請參閱[az ams 流終結點開始](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start)。

## <a name="use-sdks"></a>使用 SDK

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

請參閱完整的[JAVA 代碼示例](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)。

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

請參閱完整的[.NET 代碼示例](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112)。

---

## <a name="next-steps"></a>後續步驟

* [媒體服務 v3 OpenAPI 規範（搖擺）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [流式處理終結點操作](https://docs.microsoft.com/rest/api/media/streamingendpoints)
