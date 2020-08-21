---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: a1ed4a9e123459a697f0894ff05430c63988a70b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585403"
---
<!--Create a media services asset cLI-->

以下 Azure CLI 命令會建立新的媒體服務資產。 以您目前使用的值取代 `assetName`、`amsAccountName` 和 `resourceGroup` 的值。

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```