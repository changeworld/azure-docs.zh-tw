---
title: 使用 Azure CLI 建立認知服務資源
titleSuffix: Azure Cognitive Services
description: 藉由使用 Azure 命令列介面建立和訂閱資源，開始使用 Azure 認知服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: 認知服務, 認知智慧, 認知解決方案, ai 服務
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: e276d96e8a81b435ec4d0c270cf818555d512ae0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368894"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>快速入門：使用 Azure Command-Line 介面 (CLI 建立認知服務資源) 

您可以使用本快速入門，透過 [Azure 命令列介面 (CLI) ](/cli/azure/install-azure-cli?view=azure-cli-latest)來開始使用 Azure 認知服務。

Azure 認知服務是可搭配 REST API 和用戶端程式庫 SDK 的雲端式服務，可協助開發人員建置認知智慧應用程式，且無須直接人工智慧 (AI) 或資料科學技術或知識。 Azure 認知服務可讓開發人員使用認知解決方案，輕鬆地將認知功能新增至其應用程式，以查看、聆聽、說出、了解，甚至是開始的原因。

認知服務會由您在 Azure 訂用帳戶中建立的 Azure [資源](../azure-resource-manager/management/manage-resources-portal.md) 表示。 建立資源之後，請使用為您產生的金鑰和端點來驗證您的應用程式。

在本快速入門中，您將瞭解如何使用 [Azure 命令列介面 (CLI) ](/cli/azure/install-azure-cli?view=azure-cli-latest)來註冊 Azure 認知服務，並建立具有單一服務或多服務訂用帳戶的帳戶。 這些服務是以 Azure [資源](../azure-resource-manager/management/manage-resources-portal.md)表示，可讓您連線到一或多個 azure 認知服務 API。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>必要條件

* 有效的 Azure 訂用帳戶- [建立一個](https://azure.microsoft.com/free/cognitive-services) 免費的訂用帳戶。
* [Azure 命令列介面 (CLI) ](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>安裝 Azure CLI 並登入

安裝 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 若要登入您的本機 CLI 安裝，請執行 [az login](/cli/azure/reference-index#az-login) 命令：

```azurecli-interactive
az login
```

您也可以使用綠色的 [ **試試看** ] 按鈕，在瀏覽器中執行這些命令。

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>建立新的 Azure 認知服務資源群組

建立認知服務資源之前，您必須要有 Azure 資源群組才能包含資源。 當您建立新的資源時，可以選擇建立新的資源群組，或使用現有的資源群組。 本文說明如何建立新的資源群組。

### <a name="choose-your-resource-group-location"></a>選擇您的資源群組位置

若要建立資源，您將需要一個可供訂用帳戶使用的 Azure 位置。 您可以使用 [az account list-位置](/cli/azure/account#az-account-list-locations) 命令來取得可用位置清單。 您可以從數個位置存取大部分的認知服務。 選擇最接近您的位置，或查看可供服務使用的位置。

> [!IMPORTANT]
> * 請記住您的 Azure 位置，因為您在呼叫 Azure 認知服務時將會用到。
> * 某些認知服務的可用性會因區域而異。 如需詳細資訊，請參閱 [依區域的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)。

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

當您有 Azure 位置之後，請使用 [az group create](/cli/azure/group#az-group-create) 命令在 Azure CLI 中建立新的資源群組。

在下列範例中，請將 Azure 位置取代為 `westus2` 您訂用帳戶可用的其中一個 azure 位置。

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>選擇認知服務和定價層

建立新資源時，您必須知道您想要使用的服務類型，以及您想要的 [定價層](https://azure.microsoft.com/pricing/details/cognitive-services/) (或 sku) 。 建立資源時，您將使用此資訊和其他資訊作為參數。

### <a name="multi-service"></a>多服務

| 服務                    | 種類                      |
|----------------------------|---------------------------|
| 多種服務。 如需詳細資訊，請參閱 [定價](https://azure.microsoft.com/pricing/details/cognitive-services/) 頁面。            | `CognitiveServices`     |


> [!NOTE]
> 以下的許多認知服務都有免費層可供您試用服務。 若要使用免費層，請使用 `F0` 做為資源的 sku。

### <a name="vision"></a>視覺

| 服務                    | 種類                      |
|----------------------------|---------------------------|
| 電腦視覺            | `ComputerVision`          |
| 自訂視覺 - 預測 | `CustomVision.Prediction` |
| 自訂視覺 - 訓練   | `CustomVision.Training`   |
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

### <a name="language"></a>Language

| 服務            | 種類                |
|--------------------|---------------------|
| Form Understanding | `FormUnderstanding` |
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

您可以使用 [az cognitiveservices account list-種類](/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) 命令來尋找可用的認知服務「種類」清單：

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>將新資源新增至您的資源群組

若要建立並訂閱新的認知服務資源，請使用 [az cognitiveservices account create](/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) 命令。 此命令會將新的可計費資源新增至稍早建立的資源群組。 建立新資源時，您必須知道您想要使用的服務種類，以及其定價層 (或 sku) 和 Azure 位置：

您可以使用下列命令，為異常偵測器 (免費的) 資源建立 F0 `anomaly-detector-resource` 。

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

若要登入 Command-Line 介面的本機安裝 (CLI) ，請使用 [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令。

```azurecli-interactive
az login
```

使用 [az cognitiveservices account keys list](/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) 命令來取得認知服務資源的金鑰。

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>定價層和計費

定價層 (以及您支付的金額) 是根據您使用驗證資訊傳送的交易數目而定。 每個定價層都會指定：
* 每秒允許的交易數目上限 (TPS)。
* 在該定價層中啟用的服務功能。
* 預先定義交易數量的成本。 高於此數量將會導致服務的 [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 中指定額外費用。

## <a name="get-current-quota-usage-for-your-resource"></a>取得資源的目前配額使用量

使用 [az cognitiveservices account list-usage](/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) 命令可取得認知服務資源的使用量。

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

## <a name="see-also"></a>請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [什麼是 Azure 認知服務？](./what-are-cognitive-services.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)