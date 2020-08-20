---
title: Content Moderator Python 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，了解如何開始使用適用於 Python 的 Azure 認知服務 Content Moderator 用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 9a8076dcf23c4049ce2728c5099d691f9e865a5f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510081"
---
開始使用適用於 Python 的 Content Moderator 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 Content Moderator 是一項認知服務，可檢查文字、影像和視訊內容是否含有可能具冒犯意味、有風險或不當的資料。 找到這類資料時，服務會對內容套用適當的標籤 (旗標)。 加上旗標的內容隨後可由您的應用程式處理，以遵循法規或維護使用者應有的環境。

使用適用於 Python 的 Content Moderator 用戶端程式庫可以：

* [仲裁文字](#moderate-text)
* [使用自訂字詞清單](#use-a-custom-terms-list)
* [仲裁影像](#moderate-images)
* [使用自訂影像清單](#use-a-custom-image-list)
* [建立審核](#create-a-review)

[參考文件](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [套件 (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [範例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-content-moderator-azure-resource"></a>建立 Content Moderator Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立 Content Moderator 的資源。 您也可以：

* 在 [Azure 入口網站](https://portal.azure.com/)上檢視您的資源

從資源取得金鑰後，請為名稱分別是 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 和 `CONTENT_MODERATOR_ENDPOINT` 的金鑰及端點 URL [建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) \(部分機器翻譯\)。
 
### <a name="create-a-python-script"></a>建立 Python 指令碼

在您慣用的編輯器或 IDE 中建立新的 Python 指令碼，並加以開啟。 然後，在檔案頂端新增下列 `import` 陳述式。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

接著，為資源的端點位置建立變數，並將金鑰建立為環境變數。 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

### <a name="install-the-client-library"></a>安裝用戶端程式庫

您可以使用下列命令來安裝 Content Moderator 用戶端程式庫：

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>物件模型

下列類別會處理 Content Moderator Python 用戶端程式庫的一些主要功能。

|Name|說明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|所有 Content Moderator 功能都需要此類別。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|此類別提供對成人內容、個人資訊或人臉的影像進行分析的功能。|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|此類別提供對語言、粗話、錯誤和個人資訊的文字進行分析的功能。|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|此類別提供審核 API 的功能，包括建立作業、自訂工作流程和人工審核的方法。|

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的 Content Moderator 用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [仲裁文字](#moderate-text)
* [使用自訂字詞清單](#use-a-custom-terms-list)
* [仲裁影像](#moderate-images)
* [使用自訂影像清單](#use-a-custom-image-list)
* [建立審核](#create-a-review)

## <a name="authenticate-the-client"></a>驗證用戶端

> [!NOTE]
> 本快速入門假設您已針對 Content Moderator 金鑰和端點[建立環境變數](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

使用端點和金鑰來具現化用戶端。 使用金鑰建立 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 物件，並使用該物件與您的端點建立 [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) 物件。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>仲裁文字

下列程式碼會使用 Content Moderator 用戶端來分析文字主體，並將結果列印到主控台。 首先，在專案的根目錄建立 **text_files/** 資料夾，並新增 *content_moderator_text_moderation.txt* 檔案。 將您自己的文字新增至此檔案，或使用下列範例文字：

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

將參考新增至新的資料夾。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

然後，將下列程式碼新增至 Python 指令碼。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>使用自訂字詞清單

下列程式碼說明如何管理文字仲裁的自訂字詞清單。 您可以使用 [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) 類別建立字詞清單、管理個別字詞，以及根據該清單過濾其他文字主體。

### <a name="get-sample-text"></a>取得範例文字

若要使用此範例，您必須在專案的根目錄建立 **text_files/** 資料夾，並新增 *content_moderator_term_list.txt* 檔案。 此檔案應包含將根據字詞清單受到檢查的有機文字。 您可以使用下列範例文字：

```
This text contains the terms "term1" and "term2".
```

將參考新增至資料夾 (如果尚未定義)。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>建立清單

將下列程式碼新增至 Python 指令碼以建立自訂字詞清單，並儲存其識別碼值。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>定義清單詳細資料

您可以使用清單的識別碼來編輯其名稱和描述。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>將字詞新增至清單

下列程式碼會將字詞 `"term1"` 和 `"term2"` 新增至清單。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>取得清單中的所有字詞

您可以使用清單識別碼傳回清單中的所有字詞。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>重新整理清單索引

每當您在清單中新增或移除字詞時，都必須重新整理索引，才能使用更新的清單。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>根據清單過濾文字

自訂字詞清單的主要功能是將文字的主體與清單進行比較，並找出任何相符的字詞。 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>從清單中移除字詞

下列程式碼會從清單中移除字詞 `"term1"`。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>從清單中移除所有字詞

使用下列程式碼清除所有字詞的清單。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>刪除清單

使用下列程式碼刪除自訂字詞清單。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>仲裁影像

下列程式碼會使用 Content Moderator 用戶端以及 [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) 物件，來分析成人和猥褻內容的影像。

### <a name="get-images"></a>取得影像

定義要分析之部分影像的參考。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

然後，新增下列程式碼以逐一查看您的影像。 本節中的其餘程式碼將在此迴圈內執行。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>檢查成人/猥褻內容

下列程式碼會檢查指定 URL 中的影像是否有成人或猥褻內容，並將結果列印到主控台。 若想了解這些字詞所代表的意義，請參閱[影像仲裁概念](../../image-moderation-api.md)指南。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>檢查可見文字

下列程式碼會檢查影像中是否有可見文字內容，並將結果列印到主控台。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>檢查臉部

下列程式碼會檢查影像中是否有臉部，並將結果列印到主控台。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>使用自訂影像清單

下列程式碼說明如何管理影像仲裁的自訂影像清單。 如果您的平台經常收到同一組您想要過濾掉的影像執行個體，此功能將可發揮效用。藉由維護這些特定影像的清單，您將可改善效能。 [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) 類別可讓您建立影像清單、管理清單上的個別影像，以及將其他影像與清單進行比較。

建立下列文字變數，以儲存您將在此案例中使用的影像 URL。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> 這本身並不是正確的清單，而是將在程式碼的 `add images` 區段中新增的非正式影像清單。


### <a name="create-an-image-list"></a>建立影像清單

新增下列程式碼，以建立影像清單並儲存其識別碼的參考。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>將影像新增至清單

下列程式碼會將您所有的影像新增至清單。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

在指令碼中的任一處定義 **add_images** Helper 函式。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>取得清單中的影像

下列程式碼會列印清單中所有影像的名稱。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>更新清單詳細資料

您可以使用清單識別碼來更新清單的名稱和描述。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>取得清單詳細資料

使用下列程式碼來列印清單目前的詳細資料。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>重新整理清單索引

新增或移除影像之後，您必須先重新整理清單索引，才能用它來過濾其他影像。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>根據清單比對影像

影像清單的主要功能是要比較新的影像，並查看是否有任何相符項目。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>從清單中移除影像

下列程式碼會從清單中移除項目。 此案例中的影像是不符合清單類別的影像。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>從清單中移除所有影像

使用下列程式碼清除影像清單。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>刪除影像清單

使用下列程式碼刪除指定的影像清單。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>建立審核

您可以使用 Content Moderator Python 用戶端程式庫將內容送至[審核工具](https://contentmoderator.cognitive.microsoft.com)，讓人力仲裁者加以審核。 若要深入了解審核工具，請參閱[審核工具概念指南](../../review-tool-user-guide/human-in-the-loop.md)。

下列程式碼會使用 [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) 類別建立審核、擷取其識別碼，並在透過審核工具的 Web 入口網站接收人工輸入之後，檢查其詳細資料。

### <a name="get-review-credentials"></a>取得審核認證

首先，登入審核工具，並擷取您的小組名稱。 然後，將其指派給程式碼中的適當變數。 (選擇性) 您可以設定回呼端點，用以接收審核活動的更新。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>建立影像審核

新增下列程式碼，以建立並張貼指定影像 URL 的審核。 程式碼會儲存審核識別碼的參考。 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>取得審核詳細資料

使用下列程式碼，檢查指定審核的詳細資料。 建立審核之後，您可以自行前往審核工具並與內容互動。 如需如何執行此動作的詳細資訊，請參閱[檢閱操作指南](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)。 完成作業後，您可以再次執行此程式碼，它將會擷取審核程序的結果。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

如果您在此案例中使用了回呼端點，該端點應會收到下列格式的事件：

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `python` 命令執行應用程式。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Content Moderator Python 程式庫來執行仲裁工作。 接下來請閱讀概念性指南，以深入了解影像或其他媒體的仲裁。

> [!div class="nextstepaction"]
>[影像仲裁概念](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [什麼是 Azure Content Moderator？](../../overview.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py) 上找到。