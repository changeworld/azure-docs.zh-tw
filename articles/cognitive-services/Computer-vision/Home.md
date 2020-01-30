---
title: 什麼是電腦視覺？ - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 電腦視覺服務可供開發人員存取進階演算法，以處理影像及傳回資訊。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/27/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 17dd428f79f66bc70da47527314ad43a27955c82
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772509"
---
# <a name="what-is-computer-vision"></a>什麼是電腦視覺？

Azure 的電腦視覺服務可供開發人員存取進階演算法，以根據您感興趣的視覺功能來處理影像並傳回資訊。 例如，電腦視覺可判斷影像中是否包含成人內容，也可以尋找影像中的所有人臉。

您可以藉由原生 SDK 或直接叫用 REST API，在應用程式中使用電腦視覺。 本頁會廣泛說明電腦視覺的功用。

## <a name="computer-vision-for-digital-asset-management"></a>用於管理數位資產的電腦視覺

電腦視覺可以支援許多數位資產管理 (DAM) 案例。 DAM 是組織、儲存和抓取多媒體資產，以及管理數位權利和權限的商務程序。 例如，公司可以根據可見的標誌、臉部、物件或色彩等項目來分組和識別影像。 或者，您可以自動[產生影像的標題](./Tutorials/storage-lab-tutorial.md)並附加關鍵字，讓這些影像可供搜尋。 如需可使用認知服務、Azure 認知搜尋和智慧型報告的全能 DAM 解決方案，請參閱 GitHub 上的[知識採礦解決方案加速器指南](https://github.com/Azure-Samples/azure-search-knowledge-mining)。 如需其他 DAM 範例，請參閱[電腦視覺解決方案範本](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates)存放庫。

## <a name="analyze-images-for-insight"></a>深入分析影像

您可以分析影像，以偵測並提供與其視覺特徵和特性有關的深入解析。 下表中的所有功能是由[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 所提供。

| 動作 | 描述 |
| ------ | ----------- |
|**[標記視覺特徵](concept-tagging-images.md)**|從一組數千個可辨識的物件、生物、景象和動作，識別及標記影像中的視覺特徵。 若標記不明確或不屬於常識，API 回應會提供提示來釐清標記的內容。 標記並未限定於主體 (例如前景中的人物)，而是包含周遭環境 (室內或室外)、家具、工具、植物、動物、配件和小工具等。|
|**[偵測物件](concept-object-detection.md)**| 物件偵測與標記功能類似，但 API 會傳回每個所套用標記的週框方塊座標。 例如，如果影像包含狗、貓或人物，「偵測」作業就會列出這些物件及其在影像中的座標。 您可以使用此功能來處理影像中物件間的進一步關聯性。 當影像中有多個相同標記的執行個體時，此功能也會讓您知道。|
|**[偵測品牌](concept-brand-detection.md)**|從擁有數千個全球商標的資料庫中，識別影像或視訊內的商業品牌。 例如，您可以使用這項功能探索哪些品牌在社交媒體最受歡迎或在媒體產品位置中最常見。|
|**[將影像分類](concept-categorizing-images.md)**|使用具有父/子承襲階層的[類別分類法](Category-Taxonomy.md)來識別及分類整個影像。 類別可單獨使用，或與我們新的標記模型搭配使用。<br/>目前，英文是唯一支援影像標記和分類的語言。|
|**[說明影像](concept-describing-images.md)**|以一般人看得懂的語言，使用完整的句子產生整個影像的描述。 電腦視覺的演算法會根據在影像中識別出來的物件產生各種描述。 這些描述會個別受到評估，並產生信賴分數。 接著會傳回一份清單，並依照信賴分數由高至低排序。|
|**[偵測臉部](concept-detecting-faces.md)** |偵測影像中的臉部，並提供與每個偵測到的臉部有關的資訊。 電腦視覺會針對每個偵測到的臉部傳回座標、矩形、性別和年齡。<br/>電腦視覺提供部分的[臉部](/azure/cognitive-services/face/)服務功能。 您可以使用臉部服務進行更詳細的分析，例如臉部識別和姿勢偵測。|
|**[偵測影像類型](concept-detecting-image-types.md)**|偵測影像的關於特性，例如影像是否為線條繪圖，或影像為美工圖案的可能性。|
|**[偵測特定領域內容](concept-detecting-domain-content.md)**|使用領域模型可偵測及識別影像中的特定領域內容，例如名人和地標。 例如，如果影像包含人物，電腦視覺即可使用名人領域模型，判斷影像中偵測到的人物是否為知名人士。|
|**[偵測色彩配置](concept-detecting-color-schemes.md)**|分析影像中的用色方式。 電腦視覺可判斷影像是黑白還是彩色的，如果是彩色影像，則會找出主色和輔色。|
|**[產生縮圖](concept-generating-thumbnails.md)**|分析影像的內容，為其產生適當的縮圖。 「電腦視覺」會先產生高品質的縮圖，然後分析該影像內的物件，以判斷「關注區域」  。 接著，「電腦視覺」會裁剪影像以符合關注區域的需求。 產生的縮圖可以使用與原始影像的外觀比例不同的外觀比例來呈現，視您的需求而定。|
|**[取得關注區域](concept-generating-thumbnails.md#area-of-interest)**|分析影像的內容以傳回「關注區域」  的座標。 「電腦視覺」會傳回該區域的週框方塊座標，而不會裁剪影像並產生縮圖，因此呼叫端應用程式可以視需要修改原始影像。|

## <a name="extract-text-from-images"></a>擷取影像中的文字

您可以使用電腦視覺[閱讀 API](concept-recognizing-text.md#read-api)，將影像中列印和手寫文字擷取到電腦可讀取的字元資料流中。 讀取 API 會使用最新的模型並處理各種表層和背景上的文字，例如收據、海報、名片、信件和白板等。 目前，英文是唯一支援的語言。

您也可以使用[光學字元辨識 (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) API，以數種語言擷取列印的文字。 如有需要，OCR 會校正已辨識文字的旋轉角度，並提供每個字的框架座標。 OCR 支援 25 種語言，且會根據辨識的文字自動偵測其語言。

## <a name="moderate-content-in-images"></a>調節影像中的內容

您可以使用電腦視覺在影像中[偵測成人內容](concept-detecting-adult-content.md)，並傳回不同分類的信賴分數。 為內容加上旗標的閾值可用滑動標尺來設定，以配合您的喜好設定。

## <a name="use-containers"></a>使用容器

[使用電腦視覺容器](computer-vision-how-to-install-containers.md)，藉由在更接近資料的位置安裝標準化的 Docker 容器，於本機辨識列印和手寫的文字。

## <a name="image-requirements"></a>影像需求

電腦視覺可分析符合下列需求的影像：

- 必須以 JPEG、PNG、GIF 或 BMP 格式呈現的影像
- 影像的檔案大小必須小於 4 MB
- 影像的維度必須大於 50 x 50 像素
  - 針對讀取 API，影像的大小必須介於 50 x 50 與 10000 x 10000 像素之間。

## <a name="data-privacy-and-security"></a>資料隱私權和安全性

和所有認知服務一樣，使用電腦視覺服務的開發人員應該要了解 Microsoft 對於客戶資料的政策。 請參閱 Microsoft 信任中心上的[認知服務頁面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)，以進行深入了解。

## <a name="next-steps"></a>後續步驟

藉由遵循快速入門指南來開始使用電腦視覺：

- [快速入門：電腦視覺 .NET SDK](quickstarts-sdk/csharp-sdk.md)
- [快速入門：電腦視覺 Python SDK](quickstarts-sdk/python-sdk.md)
- [快速入門：電腦視覺 Java SDK](quickstarts-sdk/java-sdk.md)
