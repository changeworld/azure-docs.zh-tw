---
title: 使用 v3 Api 進行開發
titleSuffix: Azure Media Services
description: 瞭解使用媒體服務 v3 進行開發時，適用于實體和 Api 的規則。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79472079"
---
# <a name="develop-with-media-services-v3-apis"></a>使用媒體服務 v3 Api 進行開發

身為開發人員，您可以使用媒體服務 [REST API](https://docs.microsoft.com/rest/api/media/)，或是可讓您與 REST API 互動的用戶端程式庫，輕鬆建立、管理及維護自訂媒體工作流程。 [媒體服務 v3](https://aka.ms/ams-v3-rest-sdk) API 是以 OpenAPI 規格（先前稱為 Swagger）為基礎。

本文討論當您使用媒體服務 v3 進行開發時，適用于實體和 Api 的規則。

## <a name="accessing-the-azure-media-services-api"></a>存取 Azure 媒體服務 API

在獲得存取媒體服務資源和媒體服務 API 的授權之前，您必須先進行驗證。 媒體服務支援以[Azure Active Directory （Azure AD）為基礎的](../../active-directory/fundamentals/active-directory-whatis.md)驗證。 兩個常見的驗證選項如下：
 
* **服務主體驗證**：用來驗證服務（例如： web 應用程式、函數應用程式、邏輯應用程式、API 和微服務）。 通常使用這種驗證方法的應用程式有執行精靈服務、中介層服務或排程的工作的應用程式。 例如，對於 web 應用程式，應該一律是連接至服務主體媒體服務的中介層。
* **使用者驗證**：用來驗證使用應用程式與媒體服務資源互動的人員。 互動式應用程式應該會先提示使用者輸入使用者的認證。 例如，授權的使用者用來監控編碼工作或即時串流的管理主控台應用程式。

媒體服務 API 需要讓 REST API 要求的使用者或應用程式能夠存取媒體服務帳戶資源，並使用**參與者**或**擁有**者角色。 您可以使用**讀取器**角色來存取 API，但只能使用**取得**或**列出**作業。如需詳細資訊，請參閱[媒體服務帳戶的角色型存取控制](rbac-overview.md)。

請考慮使用 Azure 資源的受控識別，透過 Azure Resource Manager 存取媒體服務 API，而不是建立服務主體。 若要深入瞭解 Azure 資源的受控識別，請參閱[什麼是適用于 azure 資源的受控](../../active-directory/managed-identities-azure-resources/overview.md)識別。

### <a name="azure-ad-service-principal"></a>Azure AD 服務主體

如果您要建立 Azure AD 應用程式和服務主體，應用程式必須位於自己的租使用者中。 建立應用程式之後，請將媒體服務帳戶的 [**參與者**] 或 [**擁有**者] 角色存取權授與應用程式。

如果您不確定您是否有建立 Azure AD 應用程式的許可權，請參閱[必要許可權](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。

在下圖中，數位代表依時間順序排列的要求流程：

![從 Web API 使用 AAD 進行中介層應用程式驗證](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 中介層應用程式會要求具有下列參數的 Azure AD 存取權杖：  

   * Azure AD 租用戶端點。
   * 媒體服務資源 URI。
   * REST 媒體服務的資源 URI。
   * Azure AD 應用程式值：用戶端識別碼和用戶端密碼。

   若要取得所有需要的值，請參閱[使用 Azure CLI 存取 AZURE 媒體服務 API](access-api-cli-how-to.md)。

2. Azure AD 存取權杖會傳送至中介層。
4. 中介層使用該 Azure AD 權杖傳送要求至 Azure 媒體 REST API。
5. 媒體服務回傳資料給中介層。

### <a name="samples"></a>範例

請參閱下列範例，以瞭解如何使用 Azure AD 服務主體來連接：

* [與 REST 連接](media-rest-apis-with-postman.md)  
* [使用 Java 連線](configure-connect-java-howto.md)
* [使用 .NET 連線](configure-connect-dotnet-howto.md)
* [使用 Node.js 連線](configure-connect-nodejs-howto.md)
* [使用 Python 連線](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>命名慣例

Azure 媒體服務 v3 資源名稱 (例如資產、作業、轉換) 會受到 Azure Resource Manager 命名規則的約束。 根據 Azure Resource Manager，資源名稱永遠是唯一的。 因此，您可以對資源名稱使用任何唯一識別碼字串 (例如，GUID)。

媒體服務資源名稱不能包含： ' < '、' > '、'% '、' & '、'： '、' &#92; '、'？ '、'/'、' * '、' + '、'. '、單引號字元或任何控制字元。 允許所有其他字元。 資源名稱的長度上限是 260 個字元。

如需 Azure Resource Manager 命名的詳細資訊，請參閱[命名需求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)和[命名慣例](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

### <a name="names-of-filesblobs-within-an-asset"></a>資產內的檔案/blob 名稱

資產中的檔案/blob 名稱必須遵循[blob 名稱需求](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)和[NTFS 名稱需求](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)。 這些需求的原因是檔案可以從 blob 儲存體複製到本機 NTFS 磁片進行處理。

## <a name="long-running-operations"></a>長時間執行的作業

在 Azure 媒體服務 swagger 檔案`x-ms-long-running-operation`中以標記[swagger files](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)的作業是長時間執行的作業。 

如需如何追蹤非同步 Azure 作業的詳細資訊，請參閱[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)。

媒體服務具有下列長時間執行的作業：

* [建立實況活動](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [更新即時事件](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [刪除即時事件](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [開始實況活動](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [停止 LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  當停止`removeOutputsOnStop`事件時，請使用參數來刪除所有相關聯的即時輸出。  
* [重設 LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [建立 LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [刪除 LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [建立 StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [更新 StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [刪除 StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [啟動 StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [停止 StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [調整 StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

成功提交長的作業時，您會收到「202已接受」，而且必須使用傳回的作業識別碼來輪詢作業完成。

[追蹤非同步 Azure 作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)一文會深入說明如何透過回應中傳回的值，追蹤非同步 Azure 作業的狀態。

給定的即時事件或其任何相關聯的即時輸出僅支援一個長時間執行的作業。 啟動之後，長時間執行的作業必須先完成，才能在相同的 LiveEvent 或任何相關聯的即時輸出上啟動後續的長時間執行作業。 針對具有多個即時輸出的實況活動，您必須在對另一個即時輸出觸發長時間執行的作業之前，等待在一個即時輸出上完成長時間執行的作業。 

## <a name="sdks"></a>SDK

> [!NOTE]
> 不保證 Azure 媒體服務 v3 Sdk 是安全線程。 開發多執行緒應用程式時，您應該新增自己的執行緒同步處理邏輯來保護用戶端，或針對每個執行緒使用新的 AzureMediaServicesClient 物件。 您也應留意程式碼提供給用戶端 (例如 .NET 中的 HttpClient 執行個體) 的選擇性物件所引起的多執行緒處理問題。

|SDK|參考|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET 參考](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java 參考](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python 參考](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js 參考](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go 參考](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>請參閱

- [包含媒體服務事件的 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [媒體服務事件的定義](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure 媒體服務總管

[Azure 媒體服務總管](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) 是想要了解媒體服務的 Windows 客戶可用的工具。 AMSE 是 Winforms/C# 應用程式，可利用媒體服務上傳、下載、編碼、串流 VOD 和即時內容。 AMSE 工具適用於想要測試媒體服務，而不要撰寫任何程式碼的用戶端。 AMSE 程式碼會當作資源提供給想要使用媒體服務開發的客戶。

AMSE 是一個開放原始碼專案，由社群提供支援 (可將問題回報給 https://github.com/Azure/Azure-Media-Services-Explorer/issues)。 此專案採用了 [Microsoft 開放原始碼管理辦法](https://opensource.microsoft.com/codeofconduct/)。 如需詳細資訊，請參閱管理辦法[常見問題](https://opensource.microsoft.com/codeofconduct/faq/)，或opencode@microsoft.com與任何其他問題或意見聯繫。

## <a name="filtering-ordering-paging-of-media-services-entities"></a>媒體服務實體的篩選、排序、分頁

請參閱[Azure 媒體服務實體的篩選、排序和分頁](entities-overview.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>請參閱

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>後續步驟

* [使用 JAVA 連接到媒體服務](configure-connect-java-howto.md)
* [使用 .NET 連接到媒體服務](configure-connect-dotnet-howto.md)
* [使用 node.js 連接到媒體服務](configure-connect-nodejs-howto.md)
* [使用 Python 連接到媒體服務](configure-connect-python-howto.md)
