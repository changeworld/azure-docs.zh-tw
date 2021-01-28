---
title: Content Moderator .NET 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，了解如何開始使用適用於 .NET 的 Azure Content Moderator 用戶端程式庫。 在應用程式中建置內容篩選軟體，以遵循法規或維護使用者應有的環境。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: ed9d7aee5f677a5c278a985c3f8102364e94ab7e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947558"
---
開始使用適用於 .NET 的 Azure Content Moderator 用戶端程式庫。 請遵循下列步驟來安裝 NuGet 套件，並試用基本工作的程式碼範例。 

Content Moderator 是一種 AI 服務，可讓您處理可能具冒犯意味、有風險或不當的資料。 使用 AI 驅動的內容審核服務來掃描文字、影像和影片，並自動套用內容旗標。 然後，將您的應用程式與檢閱工具整合，這是人工檢閱者小組的線上仲裁環境。 在應用程式中建置內容篩選軟體，以遵循法規或維護使用者應有的環境。

使用適用於 .NET 的 Content Moderator 用戶端程式庫可以：

* 仲裁文字
* 仲裁影像
* 建立審核

[參考文件](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [範例](../../samples-dotnet.md)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或目前版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="建立 Content Moderator 資源"  target="_blank">建立 Content Moderator 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Content Moderator。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio，建立新的 .NET Core 應用程式。 

### <a name="install-the-client-library"></a>安裝用戶端程式庫 

建立新專案後，以滑鼠右鍵按一下 [方案總管] 中的專案解決方案，然後選取 [管理 NuGet 套件]，以安裝用戶端程式庫。 在開啟的套件管理員中，選取 [瀏覽]、核取 [包含發行前版本]，然後搜尋 `Microsoft.Azure.CognitiveServices.ContentModerator`。 選取版本 `2.0.0`，然後 **安裝**。 

#### <a name="cli"></a>[CLI](#tab/cli)

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `content-moderator-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*Program.cs*。

```console
dotnet new console -n content-moderator-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```console
dotnet build
```

建置輸出應該不會有警告或錯誤。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫 

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 Content Moderator 用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs) 上找到該檔案，其中包含本快速入門中的程式碼範例。

從專案目錄，在慣用的編輯器或 IDE 中開啟 *Program.cs* 檔案。 新增下列 `using` 陳述式：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

在 **Program** 類別中，為資源的金鑰和端點建立變數。

> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的 Content Moderator 資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../cognitive-services-security.md)一文。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


在應用程式的 `main()` 方法中，針對本快速入門中使用的方法新增呼叫。 您稍後會建立這些呼叫。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>物件模型

下列類別會處理 Content Moderator .NET 用戶端程式庫的一些主要功能。

|Name|說明|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|所有 Content Moderator 功能都需要此類別。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|此類別提供對成人內容、個人資訊或人臉的影像進行分析的功能。|
|[TextModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|此類別提供對語言、粗話、錯誤和個人資訊的文字進行分析的功能。|
|[審查](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|此類別提供審核 API 的功能，包括建立作業、自訂工作流程和人工審核的方法。|

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 .NET 的 Content Moderator 用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [仲裁文字](#moderate-text)
* [仲裁影像](#moderate-images)
* [建立審核](#create-a-review)

## <a name="authenticate-the-client"></a>驗證用戶端

在新方法中，使用端點和金鑰來具現化用戶端物件。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>仲裁文字

下列程式碼會使用 Content Moderator 用戶端來分析文字主體，並將結果列印到主控台。 在 **Program** 類別的根目錄中，定義輸入和輸出檔案：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

然後，在專案的根目錄中新增 *TextFile.txt* 檔案。 將您自己的文字新增至此檔案，或使用下列範例文字：

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


然後，在 **Program** 類別中的某處定義文字仲裁方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>仲裁影像

下列程式碼會使用 Content Moderator 用戶端以及 [ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation) 物件，來分析遠端影像中是否有成人和猥褻內容。

> [!NOTE]
> 您也可以分析本機影像的內容。 如需適用於本機影像的方法和作業，請參閱[參考文件](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)。

### <a name="get-sample-images"></a>取得範例影像

在 **Program** 類別的根目錄中，定義輸入和輸出檔案：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

然後，在專案的根目錄中建立輸入檔 *ImageFiles.txt*。 在此檔案中，您會新增要分析之影像的 URL &mdash; 每行一個 URL。 您可以使用下列範例影像：

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>定義協助程式類別

在 **Program** 類別中新增下列類別定義。 這個內部類別將會處理影像仲裁結果。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>定義影像仲裁方法

下列方法會逐一查看文字檔中的影像 URL、建立 **EvaluationData** 執行個體，並分析影像中是否有成人/猥褻內容、文字和人臉。 然後，它會將最終的 **EvaluationData** 執行個體新增至清單，並將傳回資料的完整清單寫入至主控台。

#### <a name="iterate-through-images"></a>逐一查看影像

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>分析內容

若要進一步了解 Content Moderator 所過濾的影像屬性，請參閱[影像仲裁概念](../../image-moderation-api.md)指南。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>將仲裁結果寫入至檔案

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>建立審核

您可以使用 Content Moderator .NET 用戶端程式庫將內容送至[審核工具](https://contentmoderator.cognitive.microsoft.com)，讓人力仲裁者加以審核。 若要深入了解審核工具，請參閱[審核工具概念指南](../../review-tool-user-guide/human-in-the-loop.md)。

本節中的方法會使用 [Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews) 類別建立審核、擷取其識別碼，並在透過審核工具的 Web 入口網站接收人工輸入之後，檢查其詳細資料。 它會將這項資訊完整記錄在輸出文字檔中。 

### <a name="get-sample-images"></a>取得範例影像

在 **Program** 類別的根目錄中宣告下列陣列。 此變數會參考用來建立審核的範例影像。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>取得審核認證

登入[審核工具](https://contentmoderator.cognitive.microsoft.com)，並擷取您的小組名稱。 然後，將其指派給 **Program** 類別中的適當變數。 (選擇性) 您可以設定回呼端點，用以接收審核活動的更新。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>定義協助程式類別

在您的 **Program** 類別中新增下列類別定義。 此類別將用來代表提交至審核工具的單一審核執行個體。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>定義協助程式方法

將下列方法新增至 **Program** 類別。 此方法會將審核查詢的結果寫入至輸出文字檔。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>定義審核建立方法

現在您已準備就緒，可定義用來處理審核建立和查詢的方法。 新增方法 **CreateReviews**，並定義下列本機變數。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>將審核發佈至審核工具

然後，新增下列程式碼以逐一查看指定的範例影像、新增中繼資料，並將其以單一批次傳送至審核工具。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

從 API 呼叫傳回的物件將會包含每個上傳影像的唯一識別碼值。 下列程式碼會剖析這些識別碼，然後用它們來查詢 Content Moderator 以取得批次中每個影像的狀態。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>取得審核詳細資料

下列程式碼會使程式等候使用者輸入。 當您在執行階段進入此步驟時，您可以自行移至[審核工具](https://contentmoderator.cognitive.microsoft.com)、確認範例影像已上傳，並與其互動。 如需如何與審核互動的詳細資訊，請參閱[審核操作指南](../../review-tool-user-guide/review-moderated-images.md)。 完成作業後，您可以按任意鍵以繼續執行程式，並擷取審核程序的結果。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

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

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

按一下 IDE 視窗頂端的 [偵錯] 按鈕，以執行應用程式。

#### <a name="cli"></a>[CLI](#tab/cli)

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Content Moderator .NET 程式庫來執行仲裁工作。 接下來請閱讀概念性指南，以深入了解影像或其他媒體的仲裁。

> [!div class="nextstepaction"]
> [影像仲裁概念](../../image-moderation-api.md)

* [什麼是 Azure Content Moderator？](../../overview.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs) 上找到。
