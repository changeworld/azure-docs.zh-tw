---
title: 使用 Azure CLI 建立認知服務資源
titleSuffix: Azure Cognitive Services
description: 藉由使用 Azure 命令列介面來建立和訂閱資源，以開始使用 Azure 認知服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 36e21a131181831c2a87c0c6d2c24c9aa6e0acf7
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245004"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>使用 Azure 命令列介面 (CLI 建立認知服務資源) 

使用本快速入門，以使用 [Azure 命令列介面 (CLI) ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)來開始使用 azure 認知服務。 認知服務是以您在 Azure 訂用帳戶中建立的 Azure [資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) 來表示。 建立資源之後，請使用為您產生的金鑰和端點來驗證您的應用程式。


在本快速入門中，您將瞭解如何使用 [Azure 命令列介面 (CLI) ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，註冊 azure 認知服務並建立具有單一服務或多服務訂用帳戶的帳戶。 這些服務是由 Azure [資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)表示，可讓您連接到一或多個 azure 認知服務 API。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>先決條件

* 有效的 Azure 訂用帳戶-免費 [建立一個](https://azure.microsoft.com/free/cognitive-services) 。
* [Azure 命令列介面 (CLI) ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>安裝 Azure CLI 並登入

安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 若要登入您的本機安裝 CLI，請執行 [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) 命令：

```azurecli-interactive
az login
```

您也可以使用綠色的 [ **試試看** ] 按鈕，在您的瀏覽器中執行這些命令。

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>建立新的 Azure 認知服務資源群組

建立認知服務資源之前，您必須擁有包含資源的 Azure 資源群組。 當您建立新的資源時，您可以選擇建立新的資源群組，或使用現有的一個。 本文說明如何建立新的資源群組。

### <a name="choose-your-resource-group-location"></a>選擇您的資源群組位置

若要建立資源，您需要有一個可供您的訂用帳戶使用的 Azure 位置。 您可以使用 [az account list-位置](/cli/azure/account#az-account-list-locations) 命令來抓取可用位置的清單。 大部分的認知服務都可以從數個位置存取。 選擇最接近您的位置，或查看可供服務使用的位置。

> [!IMPORTANT]
> * 請記住您的 Azure 位置，因為您在呼叫 Azure 認知服務時需要用到它。
> * 某些認知服務的可用性可能因地區而異。 如需詳細資訊，請參閱 [依區域的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)。  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

在您擁有 Azure 位置之後，請使用 [az group create](/cli/azure/group#az-group-create) 命令在 Azure CLI 中建立新的資源群組。

在下列範例中，將 Azure 位置取代 `westus2` 為您的訂用帳戶可用的其中一個 azure 位置。

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>選擇認知服務和定價層

建立新資源時，您必須知道您想要使用的服務「種類」，以及您想要的 [定價層](https://azure.microsoft.com/pricing/details/cognitive-services/) (或 sku) 。 建立資源時，您將使用此和其他資訊做為參數。

### <a name="multi-service"></a>多服務

| Service                    | 種類                      |
|----------------------------|---------------------------|
| 多個服務。 如需詳細資訊，請參閱 [定價](https://azure.microsoft.com/pricing/details/cognitive-services/) 頁面。            | `CognitiveServices`     |


> [!NOTE]
> 下列許多認知服務都有可用來試用服務的免費層。 若要使用免費層，請使用 `F0` 做為資源的 sku。

### <a name="vision"></a>視覺

| Service                    | 種類                      |
|----------------------------|---------------------------|
| 電腦視覺            | `ComputerVision`          |
| 自訂視覺-預測 | `CustomVision.Prediction` |
| 自訂視覺訓練   | `CustomVision.Training`   |
| 臉部                       | `Face`                    |
| 表單辨識器            | `FormRecognizer`          |
| 筆跡辨識器             | `InkRecognizer`           |

### <a name="search"></a>搜尋

| Service            | 種類                  |
|--------------------|-----------------------|
| Bing 自動建議   | `Bing.Autosuggest.v7` |
| Bing 自訂搜尋 | `Bing.CustomSearch`   |
| Bing 實體搜尋 | `Bing.EntitySearch`   |
| Bing 搜尋        | `Bing.Search.v7`      |
| Bing 拼字檢查   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>語音

| Service            | 種類                 |
|--------------------|----------------------|
| 語音服務    | `SpeechServices`     |
| 語音辨識 | `SpeakerRecognition` |

### <a name="language"></a>語言

| Service            | 種類                |
|--------------------|---------------------|
| 表單理解 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 文字分析     | `TextAnalytics`     |
| 文字翻譯   | `TextTranslation`   |

### <a name="decision"></a>決策

| Service           | 種類               |
|-------------------|--------------------|
| 異常偵測器  | `AnomalyDetector`  |
| 內容仲裁 | `ContentModerator` |
| 個人化工具      | `Personalizer`     |

您可以使用 [az cognitiveservices account list-種類](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) 命令來尋找可用的認知服務「種類」清單：

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>將新資源新增至您的資源群組

若要建立並訂閱新的認知服務資源，請使用 [az cognitiveservices account create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) 命令。 此命令會將新的可計費資源新增至稍早建立的資源群組。 建立新資源時，您必須知道您想要使用的服務「種類」，以及其定價層 (或 sku) 和 Azure 位置：

您可以使用下列命令，為異常偵測器建立 F0 (免費) 資源 `anomaly-detector-resource` 。

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>取得資源的金鑰

若要登入本機安裝命令列介面 (CLI) ，請使用 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令。

```azurecli-interactive
az login
```

使用 [az cognitiveservices account keys list](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) 命令來取得認知服務資源的金鑰。

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>定價層和計費

定價層 (和計費的金額) 取決於您使用驗證資訊傳送的交易數目。 每個定價層都會指定：
* 每秒允許的交易數上限 (TPS) 。
* 在定價層中啟用的服務功能。
* 預先定義的交易量成本。 超過此數量會導致您服務的 [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 中所指定的額外費用。

## <a name="get-current-quota-usage-for-your-resource"></a>取得資源的目前配額使用量

使用 [az cognitiveservices account list-usage](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) 命令來取得認知服務資源的使用量。

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務資源，您可以刪除它或資源群組。 刪除資源群組也會刪除與群組中的任何其他資源。

若要移除資源群組及其相關聯的資源，請使用 az group delete 命令。

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>另請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [什麼是 Azure 認知服務？](Welcome.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)
