---
title: Media Services SDK for .NET 中的重試邏輯 | Microsoft Docs
description: 本主題提供 Media Services SDK for .NET 中重試邏輯的概觀。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 63715f668438519131eba5bfff7aa38fc73267d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61094638"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Media Services SDK for .NET 中的重試邏輯  

當使用 Microsoft Azure 服務時，可能會發生暫時性錯誤。 如果發生暫時性錯誤，在大部分情況下，少數重試後作業會成功。 Media Services SDK for .NET 會實作重試邏輯來處理與例外狀況和錯誤 (Web 要求所造成) 相關聯的暫時性失敗、執行查詢、儲存變更，以及儲存體作業。  根據預設，Media Services SDK for .NET 在將例外狀況重新擲回您的應用程式之前，會執行四次重試。 然後您的應用程式中的程式碼必須適當地處理這個例外狀況。  

 以下是 Web 要求、儲存體、查詢和 SaveChanges 原則的簡短指導方針︰  

* 儲存原則適用於 blob 儲存體作業 (上傳或下載資產檔案)。  
* Web 要求原則用於泛型 Web 要求 (例如，為了取得驗證權杖以及解析使用者叢集端點)。  
* 查詢原則用於查詢 REST 的實體 (例如，mediaContext.Assets.Where(…))。  
* SaveChanges 原則用於執行任何作業，在服務內變更資料 (例如，建立實體更新實體、呼叫作業的服務函式)。  
  
  本主題會列出由 Media Services SDK for .NET 重試邏輯處理的例外狀況類型和錯誤代碼。  

## <a name="exception-types"></a>例外狀況類型
下表描述 Media Services SDK for .NET 處理或未處理可能導致暫時性失敗的某些作業。  

| 例外狀況 | Web 要求 | 存放裝置 | 查詢 | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>如需詳細資訊，請參閱 [WebException 狀態碼](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus)一節。 |是 |是 |是 |是 |
| DataServiceClientException<br/> 如需詳細資訊，請參閱 [HTTP 錯誤狀態碼](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。 |否 |是 |是 |是 |
| DataServiceQueryException<br/> 如需詳細資訊，請參閱 [HTTP 錯誤狀態碼](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。 |否 |是 |是 |是 |
| DataServiceRequestException<br/> 如需詳細資訊，請參閱 [HTTP 錯誤狀態碼](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。 |否 |是 |是 |是 |
| DataServiceTransportException |否 |否 |是 |是 |
| TimeoutException |是 |是 |是 |否 |
| SocketException |是 |是 |是 |是 |
| StorageException |否 |是 |否 |否 |
| IOException |否 |是 |否 |否 |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> WebException 狀態碼
下表顯示重試邏輯實作的 WebException 錯誤碼。 [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) 列舉定義狀態碼。  

| 狀態 | Web 要求 | 存放裝置 | 查詢 | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |是 |是 |是 |是 |
| NameResolutionFailure |是 |是 |是 |是 |
| ProxyNameResolutionFailure |是 |是 |是 |是 |
| SendFailure |是 |是 |是 |是 |
| PipelineFailure |是 |是 |是 |否 |
| ConnectionClosed |是 |是 |是 |否 |
| KeepAliveFailure |是 |是 |是 |否 |
| UnknownError |是 |是 |是 |否 |
| ReceiveFailure |是 |是 |是 |否 |
| RequestCanceled |是 |是 |是 |否 |
| 逾時 |是 |是 |是 |否 |
| ProtocolError <br/>ProtocolError 重試是由 HTTP 狀態碼處理所控制。 如需詳細資訊，請參閱 [HTTP 錯誤狀態碼](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。 |是 |是 |是 |是 |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> HTTP 錯誤狀態碼
當查詢和 SaveChanges 作業擲回 DataServiceClientException、DataServiceQueryException 或 DataServiceQueryException 時，StatusCode 屬性中會傳回 HTTP 錯誤狀態碼。  下表顯示重試邏輯實作的錯誤碼。  

| 狀態 | Web 要求 | 存放裝置 | 查詢 | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |否 |是 |否 |否 |
| 403 |否 |是<br/>處理重試等待較久。 |否 |否 |
| 408 |是 |是 |是 |是 |
| 429 |是 |是 |是 |是 |
| 500 |是 |是 |是 |否 |
| 502 |是 |是 |是 |否 |
| 503 |是 |是 |是 |是 |
| 504 |是 |是 |是 |否 |

如果您想要看一下 Media Services SDK for .NET 重試邏輯的實際實作，請參閱 [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling)。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

