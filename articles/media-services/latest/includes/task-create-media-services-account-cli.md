---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602227"
---
<!--Create a media services account -->

以下 Azure CLI 命令會建立新的媒體服務帳戶。 您可以取代下列值： `amsaccount` `storageaccountforams` (必須符合您為儲存體帳戶所提供的值) ，而且 `amsResourceGroup` (必須符合您為資源群組) 所提供的值。  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```