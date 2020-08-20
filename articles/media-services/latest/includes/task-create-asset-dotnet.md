---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: 820353ffbb5e23e27c3039ad27a8f6507b9d2167
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608748"
---
<!--Create a media services asset REST-->

下列 Azure .NET 命令會建立新的媒體服務資產。 `subscriptionID` `resourceGroup` `amsAccountName` 以您目前使用的值取代值、和。 在這裡設定以提供您的資產名稱 `assetName` 。

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```
