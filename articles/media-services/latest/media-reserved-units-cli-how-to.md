---
title: " (Mru) CLI 調整媒體保留單元"
description: 本主題顯示如何使用 CLI 搭配 Azure 媒體服務調整媒體處理。
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
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: b1c98bfa6b2cf45a59b70126001442ed80659668
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955880"
---
# <a name="how-to-scale-media-reserved-units"></a>如何調整媒體保留單元

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文說明如何 (MRSs) 調整媒體保留單元，以加快編碼速度。

## <a name="prerequisites"></a>Prerequisites

[建立媒體服務帳戶](./create-account-howto.md)。

瞭解 [媒體保留單元](concept-media-reserved-units.md)。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>使用 CLI 調整媒體保留單元

執行 `mru` 命令。

下列 [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest) 命令會使用 **計數** 和 **類型** 參數在 "amsaccount" 帳戶上設定媒體保留單元。

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>計費

系統會根據您的帳戶中布建媒體保留單元的分鐘數向您收費。 這是因為您的帳戶中是否有任何工作正在執行。 如需詳細說明，請參閱[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/)頁面的＜常見問題集＞一節。   

## <a name="next-step"></a>後續步驟

[分析影片](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>另請參閱

* [配額和限制](limits-quotas-constraints.md)
