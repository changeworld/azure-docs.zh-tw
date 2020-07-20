---
title: 環境變數
description: 設定環境變數
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: d63e902a59411a549235c955a39d7dbc4be068ba
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156746"
---
在您建立的資源中，使用金鑰和端點來建立兩個環境變數以進行驗證：

* `FORM_RECOGNIZER_KEY` - 用於驗證您要求的資源金鑰。
* `FORM_RECOGNIZER_ENDPOINT` -用來傳送 API 要求的資源端點。 它看起來像下面這樣： 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> 在 2019 年 7 月 1 日之後建立的資源端點使用下面顯示的自訂子網域格式。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

使用下列指示，在您的作業系統上設定環境變數。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

新增環境變數之後，請關閉並重新啟動主控台視窗。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

新增環境變數之後，從主控台視窗執行 `source ~/.bashrc`，讓變更生效。

#### <a name="macos"></a>[macOS](#tab/unix)

編輯 `.bash_profile`，然後新增環境變數：

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

新增環境變數之後，請從主控台視窗執行 `source .bash_profile`，讓變更生效。
***
