---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: d69cb782b3be7035b7571653dcb434e44d22da93
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377592"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [建立 Azure 語音資源](../../../../overview.md#try-the-speech-service-for-free)
> * [將原始程式檔上傳至 Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>下載並安裝 API 用戶端程式庫

若要執行範例，您必須針對透過 [Swagger](https://swagger.io) 產生的 REST API 產生 Python 程式庫。

請依照下列步驟進行安裝：

1. 移至 https://editor.swagger.io 。
1. 按一下 [檔案]  ，然後按一下 [匯入 URL]  。
1. 輸入 Swagger URL，包括語音服務訂用帳戶的區域：`https://<your-region>.cris.ai/docs/v2.0/swagger`。
1. 按一下 [產生用戶端]  ，然後選取 [Python]  。
1. 儲存用戶端程式庫。
1. 將下載的 python-client-generated.zip 解壓縮至您檔案系統中的某個位置。
1. 使用 pip 將解壓縮的 python-client 模組安裝在 Python 環境中：`pip install path/to/package/python-client`。
1. 安裝的套件名為 `swagger_client`。 您可以使用命令 `python -c "import swagger_client"` 來檢查安裝是否正常運作。

> [!NOTE]
> 由於 [Swagger 自動產生的已知錯誤](https://github.com/swagger-api/swagger-codegen/issues/7541) /(英文/)，您可能會在匯入 `swagger_client` 套件時發生錯誤。
> 修正方法是將含有該內容的行
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> 從檔案 `swagger_client/models/model.py` 中刪除，並將含有該內容的行
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> 從所安裝套件內的檔案 `swagger_client/models/inner_error.py` 中刪除。 錯誤訊息會告訴您這些檔案安裝於何處。

## <a name="install-other-dependencies"></a>安裝其他相依性

此範例使用 `requests` 程式庫。 您可以使用該命令加以安裝

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>建立與設定 HTTP 用戶端
我們需要的第一件事是具有正確基底 URL 和驗證集的 HTTP 用戶端。
在 `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)] 中插入此程式碼

## <a name="generate-a-transcription-request"></a>產生轉譯要求
接下來，我們將產生轉譯要求。 將此程式碼新增至 `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>傳送要求並檢查其狀態
現在，我們會將要求張貼到語音服務，並檢查初始的回應碼。 此回應碼只會指出服務是否已收到要求。 服務將會在回應標頭中傳回 URL，這是儲存轉譯狀態的位置。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>請等候轉譯完成
因為服務會以非同步方式處理轉譯，所以我們每隔一段時間就需要輪詢其狀態。 我們會每隔 5 秒檢查一次。

我們會列舉所有此語音服務資源正在處理的轉譯，並尋找我們建立的轉譯。

以下為輪詢程式碼，其顯示除了成功完成之外所有一切的狀態，這是我們接下來將執行的作業。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>顯示轉譯結果
一旦服務成功完成轉譯，系統會將轉譯結果儲存在另一個我們可以從狀態回應取得的 URL 中。

在此我們會取得該結果 JSON 並加以顯示。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>檢查您的程式碼
此時，您的程式碼應會如下所示：(我們已在此版本中新增一些註解) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

現在您已準備好使用語音服務來建立應用程式，並測試我們的語音辨識。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
