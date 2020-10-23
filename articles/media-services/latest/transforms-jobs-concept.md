---
title: 媒體服務中的轉換和作業
titleSuffix: Azure Media Services
description: 瞭解如何建立轉換來描述在 Azure 媒體服務中處理影片的規則。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: inhenkel
ms.openlocfilehash: 4cb4bcf5eb205d27cbca764d5ec16890a23be5c6
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426774"
---
# <a name="transforms-and-jobs-in-media-services"></a>媒體服務中的轉換和作業

本主題提供有關 [轉換](/rest/api/media/transforms) 和 [作業](/rest/api/media/jobs) 的詳細資料，並說明這些實體之間的關聯性。

## <a name="overview"></a>概觀

### <a name="transformsjobs-workflow"></a>轉換/作業工作流程

下圖顯示轉換/作業工作流程：

![Azure 媒體服務中的轉換和作業工作流程](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>一般工作流程

1. 建立轉換。
2. 在該轉換下提交作業。
3. 列出轉換。
4. 如果您不打算在未來使用轉換，請將其刪除。

#### <a name="example"></a>範例

假設您想要將所有影片的第一個畫面解壓縮為縮圖影像，您需要採取的步驟如下：

1. 定義配方或處理影片的規則：「使用影片的第一個畫面作為縮圖」。
2. 針對每個影片，您可以告訴服務：
    1. 哪裡可以找到該影片。
    2. 和寫入輸出縮圖映像的位置。

**轉換**可協助您建立一次配方 (步驟 1)，並使用該配方提交作業 (步驟 2)。

> [!NOTE]
> Datetime 類型之 **轉換** 和 **作業** 的屬性一律採用 UTC 格式。

## <a name="transforms"></a>轉換

使用**轉換**可設定視訊編碼或分析的一般工作。 每個 **轉換** 都會描述處理影片或音訊檔案的配方或工作流程。 單一轉換可套用多個規則。 例如，轉換可以指定每個視訊以指定的位元速率編碼至 MP4 檔案，並從影片第一幀畫面產生縮圖映像。 您將為每個您要加入轉換的規則新增 TransformOutput 項目。 您可以使用預設值來告訴轉換如何處理輸入媒體檔案。

### <a name="viewing-schema"></a>查看架構

在媒體服務 v3 中，預設值是 API 本身中的強型別實體。 您可以在 [OPEN API 規格 (或 Swagger) ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)中找到這些物件的「架構」定義。 您也可以在[REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset)、 [.net SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)或其他媒體服務 v3 SDK 參考檔中查看預設的定義 (例如**StandardEncoderPreset**) 。

### <a name="creating-transforms"></a>建立轉換

您可以使用 REST、CLI 或任何已發佈的 Sdk 來建立轉換。 媒體服務 v3 API 是由 Azure Resource Manager 所驅動，因此您也可以使用 Resource Manager 範本，在您的媒體服務帳戶中建立及部署轉換。 您可以使用 Azure 角色型存取控制來鎖定對轉換的存取。

### <a name="updating-transforms"></a>更新轉換

如果您需要更新 [轉換](/rest/api/media/transforms)，請使用 **更新** 作業。 它的目的是要變更描述或基礎 Transformoutputs 優先順序的優先順序。 建議您在所有進行中的工作都完成時，執行這類更新。 如果您想要重寫配方，則需要建立新的轉換。

### <a name="transform-object-diagram"></a>轉換物件圖表

下圖顯示 **轉換** 物件與其參考的物件，包括衍生關聯性。 灰色箭號會顯示作業所參考的類型，而綠色箭號則顯示類別衍生關聯性。

選取影像以查看它的完整大小。  

[![顯示轉換物件及其所參考物件的圖表，包括物件之間的類別衍生關聯性。](./media/api-diagrams/transform-small.png)](./media/api-diagrams/transform-large.png#lightbox)

## <a name="jobs"></a>工作

**作業**是媒體服務的實際要求，可將**轉換**套用至指定的輸入影片或音訊內容。 一旦建立轉換，您就可以使用媒體服務 API 或使用任何已發佈的 SDK 提交作業。 **作業**會指定輸入影片的位置、輸出的位置等資訊。 您可以使用下列內容指定輸入影片的位置： HTTPS Url、SAS Url 或 [資產](/rest/api/media/assets)。  

### <a name="job-input-from-https"></a>從 HTTPS 進行的作業輸入

如果您的內容已可透過 URL 存取，而您不需要將來源檔案儲存在 Azure (例如，從 S3) 匯入，請使用 [HTTPS 的工作輸入](job-input-from-http-how-to.md) 。 如果您有 Azure Blob 儲存體中的內容，但不需要將檔案儲存在資產中，則此方法也適用。 目前，這個方法僅支援輸入的單一檔案。

### <a name="asset-as-job-input"></a>資產作為工作輸入

如果輸入內容已在資產中，或內容儲存在本機檔案中，請使用 [資產作為工作輸入](job-input-from-local-file-how-to.md) 。 如果您想要發佈輸入資產進行串流或下載，這也是一個不錯的選項 (比方說，您想要發佈可供下載的資源，但也想要進行語音轉換文字或臉部偵測) 。 這個方法支援多檔案資產 (例如，在本機) 編碼的 MBR 串流集。

### <a name="checking-job-progress"></a>正在檢查工作進度

您可以透過事件格線的監視事件取得作業的進度和狀態。 如需詳細資料，請參閱[使用事件格線監視事件](job-state-events-cli-how-to.md)。

### <a name="updating-jobs"></a>更新作業

作業實體上的更新 [作業](/rest/api/media/jobs) 可以用來在提交作業之後修改 *描述* 和 *優先順序* 屬性。 只有在作業仍處於排入佇列的狀態時，才能有效變更「優先順序」** 屬性。 如果作業已開始處理，或已經處理完成，則變更優先順序不會有任何作用。

### <a name="job-object-diagram"></a>工作物件圖表

下圖顯示 **工作** 物件和它所參考的物件，包括衍生關聯性。

按一下影像可以完整大小檢視。  

[![顯示工作物件和它所參考之物件的圖表，包括物件之間的類別衍生關聯性。](./media/api-diagrams/job-small.png)](./media/api-diagrams/job-large.png#lightbox)

## <a name="configure-media-reserved-units"></a>設定媒體保留單位

針對媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用10個 S3 媒體保留單元來布建您的帳戶 (Mru) 。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

如需詳細資訊，請參閱[使用 CLI 調整媒體處理的規模](media-reserved-units-cli-how-to.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>另請參閱

* [錯誤碼](/rest/api/media/jobs/get#joberrorcode)
* [媒體服務實體的篩選、排序、分頁](entities-overview.md)

## <a name="next-steps"></a>後續步驟

- 開始開發之前，請參閱 [使用媒體服務 V3 api 進行開發](media-services-apis-overview.md) (包含存取 api、命名慣例等的資訊 ) 
- 請參閱下列教學課程：

    - [教學課程：編碼以 URL 為基礎的遠端檔案及串流影片](stream-files-tutorial-with-rest.md)
    - [教學課程：上傳、編碼和串流影片](stream-files-tutorial-with-api.md)
    - [教學課程：透過媒體服務 v3 分析影片](analyze-videos-tutorial-with-api.md)
