---
title: 使用 CLI 搭配 Azure 媒體服務建立篩選器 | Microsoft Docs
description: 本文說明如何使用 CLI 搭配 Azure 媒體服務 v3 來建立篩選器。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5ceff9eba0363ea2784c48e026807c53447f1e32
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89296896"
---
# <a name="creating-filters-with-cli"></a>使用 CLI 建立篩選器

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

當您提供內容給客戶 (串流即時活動或點播視訊) 時，用戶端需要的彈性可能比預設資產資訊清單檔案中所述的還多。 Azure 媒體服務可讓您為您的內容定義帳戶篩選器與資產篩選器。 

如需這項功能的詳細說明，以及使用它的案例，請參閱 [動態資訊清單](filters-dynamic-manifest-overview.md) 和 [篩選器](filters-concept.md)。

此主題說明如何為點播視訊資產設定篩選器，以及如何使用適用於第三版媒體服務的 CLI 來建立[帳戶篩選器](/cli/azure/ams/account-filter?view=azure-cli-latest)與[資產篩選器](/cli/azure/ams/asset-filter?view=azure-cli-latest)。 

> [!NOTE]
> 請務必查看 [presentationTimeRange](filters-concept.md#presentationtimerange)。

## <a name="prerequisites"></a>Prerequisites 

- [建立媒體服務帳戶](./create-account-howto.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>定義篩選器 

下列範例定義新增到最終資訊清單的曲目選取條件。 此篩選包含格式為 EC-3 的所有音軌，以及位元速率介於 0-1000000 的所有影像軌。

> [!TIP]
> 如果您打算在 REST 中定義 **篩選** ，請注意，您必須包含 "Properties" 包裝函式 JSON 物件。  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>建立帳戶篩選器

下列 [az ams account-filter](/cli/azure/ams/account-filter?view=azure-cli-latest) 命令可建立含[稍早定義](#define-a-filter)之曲目選取條件的帳戶篩選器。 

此命令可讓您傳遞包含代表軌道選取之 JSON 的選用 `--tracks` 參數。  使用 @{file} 從檔案載入 JSON。 若您在本機使用 Azure CLI，請指定完整檔案路徑：

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

此外，也請參閱[篩選器的 JSON 範例](/rest/api/media/accountfilters/createorupdate#create-an-account-filter)。

## <a name="create-asset-filters"></a>建立資產篩選器

下列 [az ams asset-filter](/cli/azure/ams/asset-filter?view=azure-cli-latest) 命令可建立含[稍早定義](#define-a-filter)之曲目選取條件的資產篩選器。 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

此外，也請參閱[篩選器的 JSON 範例](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)。

## <a name="associate-filters-with-streaming-locator"></a>將篩選準則與串流定位器建立關聯

您可以指定資產或帳戶篩選器的清單，這會套用至您的串流定位器。 [動態封裝 (串流端點) ](dynamic-packaging-overview.md)會將這份篩選清單與您的用戶端在 URL 中指定的篩選準則一起套用。 此組合會產生 [動態資訊清單](filters-dynamic-manifest-overview.md)，這是以 URL 中的篩選器和您在串流定位器上指定的篩選器為基礎。 如果您想要套用篩選，但不想公開 URL 中的篩選名稱，建議您使用這項功能。

下列 CLI 程式碼顯示如何建立串流定位器並指定 `filters` 。 這是選擇性屬性，可採用以空格分隔的資產篩選名稱和/或帳戶篩選名稱清單。

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>使用篩選的資料流程

定義篩選條件後，您的用戶端就可以在串流 URL 中使用它們。 篩選器可以套用至自適性串流通訊協定：Apple HTTP 即時串流 (HLS)、MPEG-DASH 和 Smooth Streaming。

下表顯示包含篩選器之 URL 的一些範例：

|通訊協定|範例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>後續步驟

[串流影片](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>另請參閱

[Azure CLI](/cli/azure/ams?view=azure-cli-latest)
