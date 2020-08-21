---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605976"
---
<!--Create a media services asset CLI-->

以下 Azure CLI 命令會建立新的媒體服務資產。 以您目前使用的值取代 `assetName`、`amsAccountName` 和 `resourceGroup` 的值。

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
