---
title: Azure CLI 指令碼範例 - 建立轉換 | Microsoft Docs
description: 轉換會說明用於處理視訊或音訊檔案的簡單工作流程 (通常稱為「配方」)。 本文中的 Azure CLI 指令碼會示範如何建立轉換。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e85116aaad1eecbe137ae3e470811b31d1a855f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295966"
---
# <a name="cli-example-create-a-transform"></a>CLI 範例：建立轉換

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文中的 Azure CLI 指令碼會示範如何建立轉換。 轉換會說明用於處理視訊或音訊檔案的簡單工作流程 (通常稱為「配方」)。 請一律檢查是否已有所需名稱和「配方」的轉換。 若是如此，您應該重複使用該轉換。

## <a name="prerequisites"></a>Prerequisites 

[建立媒體服務帳戶](./create-account-howto.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> 只有使用 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 時，您才能將路徑指定為指向自訂標準編碼器的預設 JSON 檔案，請參閱[使用自訂轉換進行編碼](custom-preset-cli-howto.md)的範例。
>
> 您無法在使用 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 時傳遞檔案名稱。

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>後續步驟

[az ams transform (CLI)](/cli/azure/ams/transform?view=azure-cli-latest)
