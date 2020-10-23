---
title: 使用 v3 API 開發
titleSuffix: Azure Media Services
description: 了解使用媒體服務 v3 開發時，適用於實體與 API 的規則。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: b01208c67610ff220df1654d10211472e0eed61f
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426848"
---
# <a name="develop-with-media-services-v3-apis"></a>使用媒體服務 v3 API 開發

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

身為開發人員，您可以使用媒體服務 [REST API](/rest/api/media/)，或是可讓您與 REST API 互動的用戶端程式庫，輕鬆建立、管理及維護自訂媒體工作流程。 [媒體服務 v3](https://aka.ms/ams-v3-rest-sdk) API 以 OpenAPI 規格 (先前稱為 Swagger) 作為基礎。

本文討論使用媒體服務 v3 開發時，適用於實體與 API 的規則。

## <a name="accessing-the-azure-media-services-api"></a>存取 Azure 媒體服務 API

在獲得存取媒體服務資源和媒體服務 API 的授權之前，您必須先進行驗證。 媒體服務支援 [Azure Active Directory (Azure AD) 型](../../active-directory/fundamentals/active-directory-whatis.md)驗證。 兩種常見的驗證選項為：
 
* **服務主體驗證**：用以驗證服務 (例如：Web Apps、函數應用程式、Logic Apps、API 與微服務)。 通常使用這種驗證方法的應用程式有執行精靈服務、中介層服務或排程的工作的應用程式。 例如，Web Apps 應該一律有連線到具有服務主體之媒體服務的中介層。
* **使用者驗證**：用以驗證使用應用程式與媒體服務資源互動的人員。 互動式應用程式應該先提示使用者輸入使用者的認證。 例如，授權的使用者用來監控編碼工作或即時串流的管理主控台應用程式。

媒體服務 API 需要讓提出 REST API 要求的使用者或應用程式能夠存取媒體服務帳戶資源，並使用**參與者**或**擁有者**角色。 您可以使用**讀取者**角色來存取 API，但只能使用 **Get** 或 **List** 作業。 如需詳細資訊，請參閱 [azure 角色型存取控制 (適用于媒體服務帳戶的 AZURE RBAC) ](rbac-overview.md)。

請考慮使用 Azure 資源受控識別，透過 Azure Resource Manager 存取媒體服務 API，而不是建立服務主體。 若要深入了解 Azure 資源受控識別，請參閱[什麼是 Azure 資源受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="azure-ad-service-principal"></a>Azure AD 服務主體

Azure AD 的應用程式和服務主體應位於相同的租使用者中。 建立應用程式之後，請將應用程式**參與者**或**擁有者**角色存取權授與媒體服務帳戶。

若不確定自己是否有權建立 Azure AD 應用程式，請參閱[必要權限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

下圖中，數字代表依時間順序排列的要求流程：

![從 Web API 使用 AAD 進行中介層應用程式驗證](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 中介層應用程式要求具有下列參數的 Azure AD 存取權杖：  

   * Azure AD 租用戶端點。
   * 媒體服務資源 URI。
   * REST 媒體服務的資源 URI。
   * Azure AD 應用程式值：用戶端識別碼與用戶端密碼。

   若要取得所有需要的值，請參閱[存取 Azure 媒體服務 API](./access-api-howto.md)。

2. Azure AD 存取權杖會傳送至中介層。
4. 中介層使用該 Azure AD 權杖傳送要求至 Azure 媒體 REST API。
5. 媒體服務回傳資料給中介層。

### <a name="samples"></a>範例

請參閱下列範例，了解如何使用 Azure AD 服務主體連線：

* [使用 REST 連線](media-rest-apis-with-postman.md)  
* [使用 Java 進行連線](configure-connect-java-howto.md)
* [使用 .NET 進行連線](configure-connect-dotnet-howto.md)
* [使用 Node.js 進行連線](configure-connect-nodejs-howto.md)
* [使用 Python 進行連線](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>命名慣例

Azure 媒體服務 v3 資源名稱 (例如資產、作業、轉換) 會受到 Azure Resource Manager 命名規則的約束。 根據 Azure Resource Manager，資源名稱永遠是唯一的。 因此，您可以對資源名稱使用任何唯一識別碼字串 (例如，GUID)。

媒體服務資源名稱不能包含：'<'、'>'、'%'、'&'、':'、'&#92;'、'?'、'/'、'*'、'+'、'.'、單引號字元或任何控制字元。 允許所有其他字元。 資源名稱的長度上限是 260 個字元。

如需 Azure Resource Manager 命名的詳細資訊，請參閱[命名需求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)與[命名慣例](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

### <a name="names-of-filesblobs-within-an-asset"></a>資產中的檔案/Blob 名稱

資產中的檔案/Blob 名稱必須遵循 [Blob 名稱需求](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)與 [NTFS 名稱需求](/windows/win32/fileio/naming-a-file)。 之所以會有這些需求，是因為檔案會從 Blob 儲存體複製到本機 NTFS 磁碟加以處理。

## <a name="long-running-operations"></a>長期執行作業

Azure 媒體服務 [Swagger 檔案](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)中以 `x-ms-long-running-operation` 標示的作業，是長期執行作業。 

如需如何追蹤非同步 Azure 作業的詳細資訊，請參閱[非同步作業](../../azure-resource-manager/management/async-operations.md)。

媒體服務具有下列長期執行作業：

* [建立即時事件](/rest/api/media/liveevents/create) (機器翻譯)
* [更新即時事件](/rest/api/media/liveevents/update) (機器翻譯)
* [刪除即時事件](/rest/api/media/liveevents/delete) (機器翻譯)
* [啟動即時事件](/rest/api/media/liveevents/start) (機器翻譯)
* [停止即時事件](/rest/api/media/liveevents/stop) (機器翻譯)

  當停止事件時，請使用 `removeOutputsOnStop` 參數刪除所有相關聯的即時輸出。  
* [重設即時事件](/rest/api/media/liveevents/reset) (機器翻譯)
* [建立即時輸出](/rest/api/media/liveevents/create)
* [刪除即時輸出](/rest/api/media/liveevents/delete)
* [建立串流端點](/rest/api/media/streamingendpoints/create) (機器翻譯)
* [更新串流端點](/rest/api/media/streamingendpoints/update) (機器翻譯)
* [刪除串流端點](/rest/api/media/streamingendpoints/delete) (機器翻譯)
* [啟動串流端點](/rest/api/media/streamingendpoints/start) (機器翻譯)
* [停止串流端點](/rest/api/media/streamingendpoints/stop) (機器翻譯)
* [調整串流端點](/rest/api/media/streamingendpoints/scale) (機器翻譯)

成功提交長期執行作業時，您會收到「202 已接受」訊息，而且必須使用傳回的作業識別碼來輪詢作業完成度。

[追蹤非同步 Azure 作業](../../azure-resource-manager/management/async-operations.md)一文會深入說明如何透過回應中傳回的值，追蹤非同步 Azure 作業的狀態。

僅支援指定即時事件或其任何相關聯即時輸出的一個長期執行作業。 啟動之後，長期執行作業必須先完成，才能在相同的即時事件或任何相關聯的即時輸出上，啟動後續的長期執行作業。 針對具有多個即時輸出的即時事件，您必須先等待一個即時輸出的長期執行作業完成，才能觸發另一個即時輸出的長期執行作業。 

## <a name="sdks"></a>SDK

> [!NOTE]
> Azure 媒體服務 v3 SDK 不保證是安全的執行緒。 在開發多執行緒應用程式時，您應新增本身的執行緒同步邏輯以保護用戶端，或每個執行緒都使用新的 AzureMediaServicesClient 物件。 您也應留意程式碼提供給用戶端 (例如 .NET 中的 HttpClient 執行個體) 的選擇性物件所引起的多執行緒處理問題。

|SDK|參考|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET 參考](/dotnet/api/overview/azure/mediaservices/management)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java 參考](/java/api/overview/azure/mediaservices/management)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python 參考](/python/api/overview/azure/mediaservices/management)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js 參考](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go 參考](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>另請參閱

- [包含媒體服務事件的 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [媒體服務事件的定義](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure 媒體服務總管

[Azure 媒體服務總管](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) 是想要了解媒體服務的 Windows 客戶可用的工具。 AMSE 是 Winforms/C# 應用程式，可利用媒體服務上傳、下載、編碼、串流 VOD 和即時內容。 AMSE 工具適用於想要測試媒體服務，而不要撰寫任何程式碼的用戶端。 AMSE 程式碼會當作資源提供給想要使用媒體服務開發的客戶。

AMSE 是一個開放原始碼專案，由社群提供支援 (可將問題回報給 https://github.com/Azure/Azure-Media-Services-Explorer/issues) 。 此專案採用了 [Microsoft 開放原始碼管理辦法](https://opensource.microsoft.com/codeofconduct/)。 如需詳細資訊，請參閱[管理辦法常見問題集](https://opensource.microsoft.com/codeofconduct/faq/)，如有其他問題或意見，請連絡 opencode@microsoft.com。

## <a name="filtering-ordering-paging-of-media-services-entities"></a>媒體服務實體的篩選、排序、分頁

請參閱 [Azure 媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>另請參閱

若要取得所有需要的值，請參閱[存取 Azure 媒體服務 API](./access-api-howto.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Java 連線至媒體服務](configure-connect-java-howto.md)
* [使用 .NET 連線至媒體服務](configure-connect-dotnet-howto.md)
* [使用 Node.js 連線至媒體服務](configure-connect-nodejs-howto.md)
* [使用 Python 連線至媒體服務](configure-connect-python-howto.md)