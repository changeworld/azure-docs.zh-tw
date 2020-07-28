---
title: 內嵌 Internet Analyzer 用戶端 | Microsoft Docs
description: 在此文章中，了解如何在應用程式中內嵌 Internet Analyzer JavaScript 用戶端。
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 0d4b27b85ac7bc61e14a79f29e4e26ec4973ced1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744046"
---
# <a name="embed-the-internet-analyzer-client"></a>內嵌 Internet Analyzer 用戶端

此文章將示範如何在應用程式中內嵌 JavaScript 用戶端。 您必須安裝此用戶端，才能執行測試及接收計分卡分析。 **設定檔特定的 JavaScript 用戶端會在設定第一次測試之後提供。** 您可以從該處繼續新增或移除該設定檔的測試，而不需要內嵌新的指令碼。 如需 Internet Analyzer 的詳細資訊，請參閱[概觀](internet-analyzer-overview.md)。 

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>開始之前

Internet Analyzer 需要存取 Azure 和其他 Microsoft 服務，才能正常運作。 內嵌用戶端之前，請允許網路存取 `fpc.msedge.net` 和任何預先設定的端點 URL (透過 [CLI](internet-analyzer-cli.md) 顯示)。

## <a name="find-the-client-script-url"></a>尋找用戶端指令碼 URL

設定測試之後，就能透過 Azure 入口網站或 Azure CLI 找到指令碼 URL。 如需詳細資訊，請參閱[建立 Internet Analyzer 資源](internet-analyzer-create-test-portal.md)。

選項 1。 在 Azure 入口網站中，使用[此連結](https://aka.ms/InternetAnalyzerPreviewPortal)來開啟 Azure Internet Analyzer 的預覽入口網站頁面。 藉由移至 [設定] > [設定]  ，即可瀏覽至 Internet Analyzer 設定檔以查看指令碼 URL。

選項 2。 使用 Azure CLI，檢查 `scriptFileUri` 屬性。
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>用戶端詳細資料

指令碼是專門針對您的設定檔和測試而產生的。 載入之後，指令碼將延遲 2 秒才執行。 首先，它會與 Internet Analyzer 服務聯繫，以擷取您的測試中所設定的端點清單。 然後，它會執行量測，並將計時的結果上傳回 Internet Analyzer 服務。

## <a name="client-examples"></a>用戶端範例

這些範例示範一些基本方法，以將用戶端 JavaScript 內嵌到您的網頁或應用程式。 我們使用 `0bfcb32638b44927935b9df86dcfe397` 作為指令碼 URL 的範例設定檔識別碼。

### <a name="run-on-page-load"></a>在頁面載入時執行
最簡單的方法就是在中繼標籤區塊內使用指令碼標籤。 此標籤將在每個頁面載入時執行指令碼一次。

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>後續步驟

閱讀 [Internet Analyzer 常見問題集](internet-analyzer-faq.md)
