---
title: 使用 Azure CLI 創建認知服務資源
titleSuffix: Azure Cognitive Services
description: 通過使用 Azure 命令列介面創建和訂閱資源，開始使用 Azure 認知服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221259"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>使用 Azure 命令列介面 （CLI） 創建認知服務資源

使用此快速入門可以使用[Azure 命令列介面 （CLI）](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)開始使用 Azure 認知服務。 認知服務由您在 Azure 訂閱中創建的 Azure[資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)表示。 創建資源後，請使用為您生成的鍵和終結點對應用程式進行身份驗證。 


在此快速入門中，您將學習如何使用[Azure 命令列介面 （CLI）](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)註冊 Azure 認知服務並創建具有單一服務或多服務訂閱的帳戶。 這些服務由 Azure[資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)表示，這些資源使您能夠連接到一個或多個 Azure 認知服務 API。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prerequisites

* 有效的 Azure 訂閱 - 免費[創建訂閱](https://azure.microsoft.com/free/)。
* [Azure 命令列介面 （CLI）](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>安裝 Azure CLI 並登錄 

安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 要登錄到 CLI 的本地安裝，請運行[az 登錄](https://docs.microsoft.com/cli/azure/reference-index#az-login)命令：

```azurecli-interactive
az login
```

您還可以使用綠色 **"嘗試它"** 按鈕在瀏覽器中運行這些命令。
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>創建新的 Azure 認知服務資源組

在創建認知服務資源之前，必須具有 Azure 資源組才能包含該資源。 創建新資源時，可以選擇創建新資源組或使用現有資源組。 本文演示如何創建新的資源組。

### <a name="choose-your-resource-group-location"></a>選擇資源組位置

要創建資源，需要一個可用於訂閱的 Azure 位置。 您可以使用[az 帳戶清單位置](/cli/azure/account#az-account-list-locations)命令檢索可用位置的清單。 大多數認知服務可以從多個位置訪問。 選擇離您最近的位置，或查看哪些位置可用於服務。

> [!IMPORTANT]
> * 請記住 Azure 位置，就像調用 Azure 認知服務時需要它一樣。
> * 某些認知服務的可用性可能因地區而異。 有關詳細資訊，請參閱[按區域的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)。  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

獲得 Azure 位置後，請使用[az 組創建](/cli/azure/group#az-group-create)命令在 Azure CLI 中創建新資源組。

在下面的示例中，將 Azure 位置`westus2`替換為可用於訂閱的 Azure 位置之一。

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>選擇認知服務和定價層

創建新資源時，您需要瞭解要使用的"類型"服務，以及所需的[定價層](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 sKU）。 創建資源時，您將使用此資訊和其他資訊作為參數。

### <a name="multi-service"></a>多服務

| 服務                    | 種類                      |
|----------------------------|---------------------------|
| 多種服務。 有關詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/)頁面。            | `CognitiveServices`     |


> [!NOTE]
> 下面的許多認知服務都有一個免費的套餐，您可以使用它來嘗試該服務。 要使用免費套餐，請使用`F0`作為資源的 sku。

### <a name="vision"></a>視覺

| 服務                    | 種類                      |
|----------------------------|---------------------------|
| 電腦視覺            | `ComputerVision`          |
| 自訂視覺 - 預測 | `CustomVision.Prediction` |
| 自訂視覺 - 培訓   | `CustomVision.Training`   |
| 臉部                       | `Face`                    |
| 表單辨識器            | `FormRecognizer`          |
| 筆跡辨識器             | `InkRecognizer`           |

### <a name="search"></a>搜尋

| 服務            | 種類                  |
|--------------------|-----------------------|
| Bing 自動建議   | `Bing.Autosuggest.v7` |
| Bing 自訂搜尋 | `Bing.CustomSearch`   |
| Bing 實體搜尋 | `Bing.EntitySearch`   |
| Bing 搜尋        | `Bing.Search.v7`      |
| Bing 拼字檢查   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>語音

| 服務            | 種類                 |
|--------------------|----------------------|
| 語音服務    | `SpeechServices`     |
| 語音辨識 | `SpeakerRecognition` |

### <a name="language"></a>語言

| 服務            | 種類                |
|--------------------|---------------------|
| 表單理解 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 文字分析     | `TextAnalytics`     |
| 文字翻譯   | `TextTranslation`   |

### <a name="decision"></a>決策

| 服務           | 種類               |
|-------------------|--------------------|
| 異常偵測器  | `AnomalyDetector`  |
| 內容仲裁 | `ContentModerator` |
| 個人化工具      | `Personalizer`     |

您可以使用[az 認知服務帳戶類型](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds)命令查找可用認知服務"種類"的清單：

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>向資源組添加新資源

要創建和訂閱新的認知服務資源，請使用 az[認知服務帳戶創建](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create)命令。 此命令向之前創建的資源組添加新計費資源。 創建新資源時，您需要瞭解要使用的"類型"服務及其定價層（或 sKU）和 Azure 位置：

您可以為異常檢測器創建 F0（免費）資源，該`anomaly-detector-resource`資源使用下面的命令命名。

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>獲取資源金鑰

要登錄到命令列介面 （CLI） 的本地安裝，請使用[az 登錄](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)命令。

```azurecli-interactive
az login
```

使用[az 認知服務帳戶金鑰清單](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list)命令獲取認知服務資源的金鑰。

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>定價層和計費

定價層（以及您獲得計費的金額）基於您使用身份驗證資訊發送的事務數。 每個定價層指定：
* 每秒允許事務的最大數量 （TPS）。
* 在定價層內啟用的服務功能。
* 預定義交易記錄量的成本。 超過此金額將導致服務[定價詳細資訊](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中指定的額外費用。

## <a name="get-current-quota-usage-for-your-resource"></a>獲取資源當前配額使用方式

使用[az 認知服務帳戶清單使用](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage)命令獲取認知服務資源的使用方式。

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>清除資源

如果要清理和刪除認知服務資源，可以將其或資源組刪除。 刪除資源組還會刪除該組中包含的任何其他資源。

要刪除資源組及其關聯資源，請使用 az 組刪除命令。

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>另請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [什麼是 Azure 認知服務？](Welcome.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)
