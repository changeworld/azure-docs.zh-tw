---
title: 媒體服務中的轉換和工作
titleSuffix: Azure Media Services
description: 瞭解如何創建轉換以描述在 Azure 媒體服務中處理視頻的規則。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571228"
---
# <a name="transforms-and-jobs-in-media-services"></a>媒體服務中的轉換和工作

本主題提供有關[轉換](https://docs.microsoft.com/rest/api/media/transforms)和[作業](https://docs.microsoft.com/rest/api/media/jobs)的詳細資訊，並解釋這些實體之間的關係。

## <a name="overview"></a>總覽

### <a name="transformsjobs-workflow"></a>轉換/作業工作流

下圖顯示了轉換/作業工作流：

![Azure 媒體服務中的轉換和作業工作流](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>一般工作流程

1. 創建轉換。
2. 根據該轉換提交作業。
3. 列出轉換。
4. 如果您不打算將來使用該轉換，請刪除該轉換。

#### <a name="example"></a>範例

假設您要提取所有視頻的第一幀作為縮略圖圖像 - 您將採取的步驟是：

1. 定義用於處理視頻的配方或規則："使用視頻的第一幀作為縮略圖"。
2. 對於每個視頻，您將告訴服務：
    1. 在哪裡可以找到該視頻。
    2. 和寫入輸出縮圖映像的位置。

**轉換**可協助您建立一次配方 (步驟 1)，並使用該配方提交作業 (步驟 2)。

> [!NOTE]
> **轉換**屬性和日期時間類型的**作業**始終採用 UTC 格式。

## <a name="transforms"></a>轉換

使用**轉換**可設定視訊編碼或分析的一般工作。 每個**轉換**都描述了用於處理視頻或音訊檔的任務配方或工作流。 單一轉換可套用多個規則。 例如，轉換可以指定每個視訊以指定的位元速率編碼至 MP4 檔案，並從影片第一幀畫面產生縮圖映像。 您將為每個您要加入轉換的規則新增 TransformOutput 項目。 您可以使用預設告訴 Transform 如何處理輸入媒體檔案。

### <a name="viewing-schema"></a>查看架構

在媒體服務 v3 中，預設是 API 本身中的強型別實體。 您可以在[Open API 規範（或 Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)中找到這些物件的"架構"定義。 您還可以在[REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [、.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)或其他媒體服務 v3 SDK 參考文檔中查看預設定義（如**標準編碼預設**）。

### <a name="creating-transforms"></a>創建轉換

您可以使用 REST、CLI 或任何已發佈的 SDK 創建轉換。 媒體服務 v3 API 是由 Azure Resource Manager 所驅動，因此您也可以使用 Resource Manager 範本，在您的媒體服務帳戶中建立及部署轉換。 角色型存取控制可用於鎖定轉換的存取權。

### <a name="updating-transforms"></a>更新轉換

如果需要更新[轉換](https://docs.microsoft.com/rest/api/media/transforms)，請使用 **"更新"** 操作。 它旨在更改描述或基礎轉換輸出的優先順序。 建議在所有正在進行的作業完成後執行此類更新。 如果要重寫配方，則需要創建新的轉換。

### <a name="transform-object-diagram"></a>變換物件關係圖

下圖顯示了**Transform**物件及其引用的物件，包括派生關係。 灰色箭頭顯示作業引用的類型和綠色箭頭顯示類派生關係的類型。

選擇圖像以查看其全尺寸。  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>工作

**作業**是媒體服務將**轉換**應用於給定輸入視頻或音訊內容的實際請求。 一旦建立轉換，您就可以使用媒體服務 API 或使用任何已發佈的 SDK 提交作業。 **作業**會指定輸入影片的位置、輸出的位置等資訊。 您可以使用以下條件指定輸入視頻的位置：HTTPS URL、SAS URL 或[資產](https://docs.microsoft.com/rest/api/media/assets)。  

### <a name="job-input-from-https"></a>來自 HTTPS 的作業輸入

如果內容已經通過 URL 訪問，並且不需要在 Azure 中存儲原始檔案（例如，從 S3 導入），請使用[HTTPS 的作業輸入](job-input-from-http-how-to.md)。 如果 Azure Blob 存儲中包含內容，但不需要檔位於資產中，則此方法也適用。 目前，此方法僅支援單個檔進行輸入。

### <a name="asset-as-job-input"></a>資產作為作業輸入

如果輸入內容已位於資產中或內容存儲在本地檔中，則使用[資產作為作業輸入](job-input-from-local-file-how-to.md)。 如果您計畫發佈用於資料流或下載的輸入資產（例如，您希望發佈 mp4 進行下載，但還要進行語音到文本或人臉檢測），這也是一個不錯的選擇。 此方法支援多檔資產（例如，在本地編碼的 MBR 流集）。

### <a name="checking-job-progress"></a>檢查作業進度

您可以透過事件格線的監視事件取得作業的進度和狀態。 如需詳細資料，請參閱[使用事件格線監視事件](job-state-events-cli-how-to.md)。

### <a name="updating-jobs"></a>更新作業

[作業](https://docs.microsoft.com/rest/api/media/jobs)實體上的"更新"操作可用於在提交作業後修改*說明*和*優先順序*屬性。 只有在作業仍處於排入佇列的狀態時，才能有效變更「優先順序」** 屬性。 如果作業已開始處理，或已經處理完成，則變更優先順序不會有任何作用。

### <a name="job-object-diagram"></a>工作物件圖

下圖顯示了**作業**物件及其引用的物件，包括派生關係。

按一下影像可以完整大小檢視。  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>設定媒體保留單位

對於由媒體服務 v3 或視頻索引子觸發的音訊分析和視頻分析作業，強烈建議使用 10 個 S3 媒體保留單位 （MRUS） 預配您的帳戶。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

如需詳細資訊，請參閱[使用 CLI 調整媒體處理的規模](media-reserved-units-cli-how-to.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>另請參閱

* [錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [媒體服務實體的篩選、排序、分頁](entities-overview.md)

## <a name="next-steps"></a>後續步驟

- 在開始開發之前，請查看[使用媒體服務 v3 API 進行開發](media-services-apis-overview.md)（包括有關訪問 API、命名約定等的資訊）
- 請查看以下教程：

    - [教程：基於 URL 對遠端檔進行編碼並資料流視頻](stream-files-tutorial-with-rest.md)
    - [教程：上傳、編碼和資料流視頻](stream-files-tutorial-with-api.md)
    - [教程：使用媒體服務 v3 分析視頻](analyze-videos-tutorial-with-api.md)
