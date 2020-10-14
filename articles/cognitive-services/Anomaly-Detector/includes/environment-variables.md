---
title: 異常偵測器環境變數
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: mbullwin
ms.openlocfilehash: 74e5373ebafbd39748f8b002069eaa6d732be069
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018429"
---
### <a name="create-an-environment-variable"></a>建立環境變數

>[!NOTE]
> 在 2019 年 7 月 1 日之後為非試用資源建立的端點會使用自訂的子網域格式，如下所示。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

在您建立的資源中，使用金鑰和端點來建立兩個環境變數以進行驗證：

* `ANOMALY_DETECTOR_KEY` - 用於驗證您要求的資源金鑰。
* `ANOMALY_DETECTOR_ENDPOINT` -用來傳送 API 要求的資源端點。 它看起來像下面這樣： 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

請使用適合您作業系統的指示。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

新增環境變數之後，請重新啟動主控台視窗。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

新增環境變數之後，從主控台視窗執行 `source ~/.bashrc`，讓變更生效。

#### <a name="macos"></a>[macOS](#tab/unix)

編輯 `.bash_profile`，然後新增環境變數：

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

新增環境變數之後，從主控台視窗執行 `source .bash_profile`，讓變更生效。

***