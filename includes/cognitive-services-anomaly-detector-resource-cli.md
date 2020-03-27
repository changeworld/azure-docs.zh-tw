---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "74483020"
---
藉由建立下列其中一項 Azure 資源來開始使用 Anomaly Detector 服務。

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">建立試用資源 (在新索引標籤中開啟)</a>
    * 不需要 Azure 訂用帳戶： 
    * 可免費使用 7 天。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得試用金鑰與端點。 
    * 如果您想要試用 Anomaly Detector，但沒有 Azure 訂用帳戶，這是很好的選擇。

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">建立 Anomaly Detector 資源 (在新索引標籤中開啟)</a>：
    * 您可以透過 Azure 入口網站取得該資源，直到將其刪除為止。
    * 使用免費定價層來試用服務，之後可升級至付費層以用於實際執行環境。



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