---
title: 使用 v3 API 進行開發
titleSuffix: Azure Media Services
description: 瞭解使用媒體服務 v3 開發時適用于實體和 API 的規則。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472079"
---
# <a name="develop-with-media-services-v3-apis"></a>使用媒體服務 v3 API 進行開發

身為開發人員，您可以使用媒體服務 [REST API](https://docs.microsoft.com/rest/api/media/)，或是可讓您與 REST API 互動的用戶端程式庫，輕鬆建立、管理及維護自訂媒體工作流程。 [媒體服務 v3](https://aka.ms/ams-v3-rest-sdk) API 基於 OpenAPI 規範（以前稱為 Swagger）。

本文討論使用媒體服務 v3 開發時適用于實體和 API 的規則。

## <a name="accessing-the-azure-media-services-api"></a>訪問 Azure 媒體服務 API

在獲得存取媒體服務資源和媒體服務 API 的授權之前，您必須先進行驗證。 媒體服務支援[基於 Azure 活動目錄 （Azure AD） 的](../../active-directory/fundamentals/active-directory-whatis.md)身份驗證。 兩個常見的身份驗證選項是：
 
* **服務主體身份驗證**：用於對服務進行身份驗證（例如：Web 應用、函數應用、邏輯應用、API 和微服務）。 通常使用這種驗證方法的應用程式有執行精靈服務、中介層服務或排程的工作的應用程式。 例如，對於 Web 應用，應始終有一個中介層，該層與服務主體連接到媒體服務。
* **使用者身份驗證**：用於驗證使用應用與媒體服務資源交互的人員。 互動式應用應首先提示使用者輸入使用者的憑據。 例如，授權的使用者用來監控編碼工作或即時串流的管理主控台應用程式。

媒體服務 API 要求發出 REST API 請求的使用者或應用有權訪問媒體服務帳戶資源並使用**參與者**或**擁有者**角色。 可以使用**Reader**角色訪問 API，但只有**獲取**或**清單**操作可用。有關詳細資訊，請參閱[媒體服務帳戶的基於角色的存取控制](rbac-overview.md)。

請考慮使用 Azure 資源的託管標識通過 Azure 資源管理器訪問媒體服務 API，而不是創建服務主體。 要瞭解有關 Azure 資源的託管標識詳細資訊，請參閱[Azure 資源的託管標識是什麼](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="azure-ad-service-principal"></a>Azure AD 服務主體

如果要創建 Azure AD 應用和服務主體，則該應用必須位於其自己的租戶中。 創建應用後，向應用**參與者**或**擁有者**角色授予對媒體服務帳戶的存取權限。

如果不確定是否具有創建 Azure AD 應用的許可權，請參閱["必需許可權](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)"。

在下圖中，數位按時間順序表示請求的流：

![從 Web API 使用 AAD 進行中介層應用身份驗證](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 中介層應用請求具有以下參數的 Azure AD 訪問權杖：  

   * Azure AD 租用戶端點。
   * 媒體服務資源 URI。
   * REST 媒體服務的資源 URI。
   * Azure AD 應用值：用戶端 ID 和用戶端機密。

   要獲取所有所需的值，請參閱[使用 Azure CLI 訪問 Azure 媒體服務 API。](access-api-cli-how-to.md)

2. Azure AD 存取權杖會傳送至中介層。
4. 中介層使用該 Azure AD 權杖傳送要求至 Azure 媒體 REST API。
5. 媒體服務回傳資料給中介層。

### <a name="samples"></a>範例

請參閱以下示例，這些示例演示如何與 Azure AD 服務主體連接：

* [與 REST 連接](media-rest-apis-with-postman.md)  
* [使用 Java 進行連線](configure-connect-java-howto.md)
* [使用 .NET 連線](configure-connect-dotnet-howto.md)
* [使用 Node.js 連線](configure-connect-nodejs-howto.md)
* [使用 Python 進行連線](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>命名慣例

Azure 媒體服務 v3 資源名稱 (例如資產、作業、轉換) 會受到 Azure Resource Manager 命名規則的約束。 根據 Azure Resource Manager，資源名稱永遠是唯一的。 因此，您可以對資源名稱使用任何唯一識別碼字串 (例如，GUID)。

媒體服務資源名稱不能包括："<"，">"，"%"，"&"，"&#92;"，"？"，"/"，"*，"*，"*，""."，""."，單引號字元，或任何控制字元。 允許所有其他字元。 資源名稱的長度上限是 260 個字元。

有關 Azure 資源管理器命名的詳細資訊，請參閱[命名要求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)和[命名約定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

### <a name="names-of-filesblobs-within-an-asset"></a>資產中檔/blob 的名稱

資產中檔/blob 的名稱必須同時遵循[blob 名稱要求](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)和[NTFS 名稱要求](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)。 這些要求的原因是檔可以從 Blob 存儲複製到本地 NTFS 磁片進行處理。

## <a name="long-running-operations"></a>長時間運行的操作

在 Azure`x-ms-long-running-operation`媒體服務[揮霍檔中](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)標記的操作是長時間運行的操作。 

有關如何跟蹤非同步 Azure 操作的詳細資訊，請參閱[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)。

媒體服務具有以下長時間運行的操作：

* [創建即時活動](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [更新即時事件](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [刪除即時事件](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [啟動即時活動](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [停止直播活動](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  使用`removeOutputsOnStop`參數在停止事件時刪除所有關聯的即時輸出。  
* [重置即時事件](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [創建即時輸出](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [刪除即時輸出](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [建立 StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [更新流式處理終結點](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [刪除流式處理終結點](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [啟動 StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [停止流式處理終結點](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [縮放流式處理終結點](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

成功提交長操作後，您將收到一個"202 已接受"，並且必須使用返回的操作 ID 輪詢操作完成。

[追蹤非同步 Azure 作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)一文會深入說明如何透過回應中傳回的值，追蹤非同步 Azure 作業的狀態。

對於給定的即時事件或其任何關聯的即時輸出，僅支援一個長時間運行的操作。 啟動後，必須完成長時間運行的操作，然後才能在同一 LiveEvent 或任何關聯的即時輸出上啟動後續長時間運行的操作。 對於具有多個即時輸出的即時事件，您必須等待在一個即時輸出上完成長時間運行的操作，然後再在另一個即時輸出上觸發長時間運行的操作。 

## <a name="sdks"></a>SDK

> [!NOTE]
> Azure 媒體服務 v3 SDK 不保證是執行緒安全的。 開發多執行緒應用時，應添加自己的執行緒同步邏輯以保護用戶端或使用每個執行緒的新 AzureMediaServicesClient 物件。 您也應留意程式碼提供給用戶端 (例如 .NET 中的 HttpClient 執行個體) 的選擇性物件所引起的多執行緒處理問題。

|SDK|參考資料|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET 參考](https://aka.ms/ams-v3-dotnet-ref)|
|[JAVA SDK](https://aka.ms/ams-v3-java-sdk)|[Java 參考](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python 參考](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js 參考](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go 參考](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>另請參閱

- [包含媒體服務事件的 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [媒體服務事件的定義](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure 媒體服務總管

[Azure 媒體服務總管](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) 是想要了解媒體服務的 Windows 客戶可用的工具。 AMSE 是 Winforms/C# 應用程式，可利用媒體服務上傳、下載、編碼、串流 VOD 和即時內容。 AMSE 工具適用於想要測試媒體服務，而不要撰寫任何程式碼的用戶端。 AMSE 程式碼會當作資源提供給想要使用媒體服務開發的客戶。

AMSE 是一個開放原始碼專案，由社群提供支援 (可將問題回報給 https://github.com/Azure/Azure-Media-Services-Explorer/issues)。 此專案採用了 [Microsoft 開放原始碼管理辦法](https://opensource.microsoft.com/codeofconduct/)。 有關詳細資訊，請參閱[行為準則常見問題解答](https://opensource.microsoft.com/codeofconduct/faq/)或聯繫opencode@microsoft.com任何其他問題或意見。

## <a name="filtering-ordering-paging-of-media-services-entities"></a>媒體服務實體的篩選、排序、分頁

請參閱[Azure 媒體服務實體的篩選、排序和分頁](entities-overview.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>另請參閱

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>後續步驟

* [使用 JAVA 連接到媒體服務](configure-connect-java-howto.md)
* [使用 .NET 連接到媒體服務](configure-connect-dotnet-howto.md)
* [使用 Node.js 連接到媒體服務](configure-connect-nodejs-howto.md)
* [使用 Python 連接到媒體服務](configure-connect-python-howto.md)
