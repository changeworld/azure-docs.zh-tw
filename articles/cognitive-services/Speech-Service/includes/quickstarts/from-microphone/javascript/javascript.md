---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 1a1207b056a0cd86d1855d990a8857ef8d9752e4
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82979545"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立 Azure 語音資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md)
> * 確定您可以存取麥克風以擷取音訊

## <a name="create-a-new-website-folder"></a>建立新的網站資料夾

建立新的空白資料夾。 如果您想要在 Web 伺服器上裝載範例，請確定 Web 伺服器可以存取該資料夾。

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>將適用於 JavaScript 的語音 SDK 解壓縮到該資料夾

下載 [.zip 套件](https://aka.ms/csspeech/jsbrowserpackage)形式的語音 SDK，並將它解壓縮到新建立的資料夾。 這會解壓縮出兩個檔案，`microsoft.cognitiveservices.speech.sdk.bundle.js` 和 `microsoft.cognitiveservices.speech.sdk.bundle.js.map`。
第二個檔案是選擇性的，並適用於對 SDK 程式碼進行偵錯。

## <a name="create-an-indexhtml-page"></a>建立 index.html 網頁

在資料夾中建立名為 `index.html` 的新檔案，並使用文字編輯器開啟此檔案。

1. 建立下列 HTML 基本架構：

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-from-mic.html)]

## <a name="create-the-token-source-optional"></a>建立權杖來源 (選擇性)

如果想要在 Web 伺服器上裝載網頁，可以選擇性提供示範應用程式的權杖來源。
這樣一來，訂用帳戶金鑰將永遠不會離開您的伺服器，同時可讓使用者不需要輸入任何授權代碼就能使用語音功能。

建立名為 `token.php` 的新檔案。 在此範例中，我們會假設您的 Web 伺服器支援 PHP 指令碼語言。 輸入下列程式碼：

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> 授權權杖的存留期是有限的。
> 這個簡化範例不會說明如何自動重新整理授權權杖。 如果您是使用者，可以手動重新載入或按 F5 以重新整理頁面。

## <a name="build-and-run-the-sample-locally"></a>在本機建置並執行範例

若要啟動應用程式，請按兩下 index.html 檔案或使用最愛的網頁瀏覽器開啟 index.html。 它將會提供簡單的 GUI，供您輸入訂用帳戶金鑰與[區域](../../../../regions.md)，並使用麥克風觸發辨識。

> [!NOTE]
> 此方法無法在 Safari 瀏覽器上運作。
> 在 Safari 上，範例網頁必須裝載在 Web 伺服器上；Safari 不允許從本機檔案載入的網站使用麥克風。

## <a name="build-and-run-the-sample-via-a-web-server"></a>透過 Web 伺服器建置並執行範例

若要啟動應用程式，請開啟您最愛的網頁瀏覽器並指向資料夾裝載所在的公用 URL，輸入您的[地區](../../../../regions.md)，並使用麥克風觸發辨識。 如果已設定，它將會從您的權杖來源取得權杖。


## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

