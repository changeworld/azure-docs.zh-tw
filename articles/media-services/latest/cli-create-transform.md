---
title: Azure CLI 指令碼範例 - 建立轉換 | Microsoft Docs
description: 轉換會說明用於處理視訊或音訊檔案的簡單工作流程 (通常稱為「配方」)。 本文中的 Azure CLI 指令碼會示範如何建立轉換。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: c21a16d043f972042949d6340985774741b3df6a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74888593"
---
# <a name="cli-example-create-a-transform"></a>CLI 範例：建立轉換

本文中的 Azure CLI 指令碼會示範如何建立轉換。 轉換會說明用於處理視訊或音訊檔案的簡單工作流程 (通常稱為「配方」)。 請一律檢查是否已有所需名稱和「配方」的轉換。 若是如此，您應該重複使用該轉換。

## <a name="prerequisites"></a>Prerequisites 

[建立媒體服務帳戶](create-account-cli-how-to.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> 只有使用 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 時，您才能將路徑指定為指向自訂標準編碼器的預設 JSON 檔案，請參閱[使用自訂轉換進行編碼](custom-preset-cli-howto.md)的範例。
>
> 您無法在使用 [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 時傳遞檔案名稱。

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>後續步驟

[az ams transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
