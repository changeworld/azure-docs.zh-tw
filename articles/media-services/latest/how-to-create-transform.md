---
title: Azure CLI 腳本範例-建立轉換
description: 轉換會說明用於處理視訊或音訊檔案的簡單工作流程 (通常稱為「配方」)。 本文中的 Azure CLI 指令碼會示範如何建立轉換。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: adbd7deccf32312f67cff7b92ff7813036e9b1b3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898523"
---
# <a name="create-a-transform"></a>建立轉換

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文中的 Azure CLI 指令碼會示範如何建立轉換。 轉換會說明用於處理視訊或音訊檔案的簡單工作流程 (通常稱為「配方」)。 請一律檢查是否已有所需名稱和「配方」的轉換。 若是如此，您應該重複使用該轉換。

## <a name="prerequisites"></a>Prerequisites

[建立媒體服務帳戶](./create-account-howto.md)。

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> 只有使用 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 時，您才能將路徑指定為指向自訂標準編碼器的預設 JSON 檔案，請參閱[使用自訂轉換進行編碼](custom-preset-cli-howto.md)的範例。
>
> 您無法在使用 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 時傳遞檔案名稱。

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>後續步驟

[更多關於轉換和作業的資訊](transforms-jobs-concept.md)
