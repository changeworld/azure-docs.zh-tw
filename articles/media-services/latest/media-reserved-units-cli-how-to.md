---
title: 使用 CLI 調整媒體保留單元 - Azure | Microsoft Docs
description: 本主題顯示如何使用 CLI 搭配 Azure 媒體服務調整媒體處理。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9f0a7425fc09d391828a748832f662f02c6022cf
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970789"
---
# <a name="scaling-media-processing"></a>調整媒體處理

Azure 媒體服務可讓您藉由管理媒體保留單元 (MRU)，在帳戶中調整媒體處理方式。 Mru 決定媒體處理工作的處理速度。 您可以選擇下列的保留單元類型：**S1**、**S2** 或 **S3**。 例如，在執行相同編碼作業的前提下，使用 **S2** 保留單元類型的速度會比 **S1** 類型快。 

除了指定保留單元類型之外，您還可以指定使用保留單元來佈建帳戶。 佈建的保留單元數目可決定指定帳戶中可同時處理的媒體工作數目。 例如，如果帳戶有五個保留單元，則只要有工作需要處理，就會同時執行五個媒體工作。 剩餘的工作會在佇列中等待，當執行中的工作完成時，就循序地挑選來處理。 如果帳戶未佈建任何保留單元，則會循序地挑選工作。 在此情況下，一件工作完成與下一件工作開始之間的等待時間，視系統中的資源可用性而定。

## <a name="choosing-between-different-reserved-unit-types"></a>選擇不同的保留單元類型

下表可協助您在不同編碼速度之間進行選擇時做出決定。 它也針對[可供您下載的影片](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)提供幾個基準測試案例來執行您自己的測試：

|RU 類型|狀況|[7 分鐘 1080p 視訊](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)的結果範例|
|---|---|---|
| **S1**|單一位元速率編碼。 <br/>在 SD或如下解決方法的檔案、對時間不敏感、低成本。|使用「H264 單一位元速率 SD 16x9」編碼為單一位元速率 SD 解析的檔檔案大約需要7分鐘的時間。|
| **S2**|單一位元速率和多重位元速率編碼。<br/>SD 和 HD 編碼的一般使用方式。|具有「H264 單一位元速率720p」預設值的編碼需要大約6分鐘的時間。<br/><br/>具有「H264 多重位元速率720p」預設值的編碼需要大約12分鐘的時間。|
| **S3**|單一位元速率和多重位元速率編碼。<br/>Full HD 和 4K 解析度影片。 對時間敏感、周轉時間更快的編碼。|具有「H264 單一位元速率1080p」預設值的編碼需要大約3分鐘的時間。<br/><br/>具有「H264 多重位元速率 1080p」預設值的編碼需要大約 8 分鐘。|

## <a name="considerations"></a>考量

* 針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 S3 單元類型。
* 如果使用共用的集區，也就是沒有任何保留單元，則編碼工作會和 S1 RU 有相同的效能。 不過，您的工作在已排入佇列的狀態下可以花費的時間沒有上限，而且在任何時候，最多只會執行一個工作。

本文的其餘部分將說明如何使用[媒體服務 V3 CLI](https://aka.ms/ams-v3-cli-ref)來調整 mru。

> [!NOTE]
> 針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 MRU 佈建帳戶。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

## <a name="prerequisites"></a>Prerequisites 

[建立媒體服務帳戶](create-account-cli-how-to.md)。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>使用 CLI 調整媒體保留單元

執行 `mru` 命令。

下列 [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) 命令會使用**計數**和**類型**參數在 "amsaccount" 帳戶上設定媒體保留單元。

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>計費

系統會根據您帳戶中布建媒體保留單元的分鐘數向您收費。 這會與您的帳戶中是否有任何執行中的作業無關。 如需詳細說明，請參閱[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/)頁面的＜常見問題集＞一節。   

## <a name="next-step"></a>後續步驟

[分析影片](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>另請參閱

* [配額和限制](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
