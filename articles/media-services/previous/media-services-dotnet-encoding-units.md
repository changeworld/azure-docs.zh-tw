---
title: 藉由新增編碼單元來調整媒體處理的規模 - Azure | Microsoft Docs
description: 本文示範如何使用 Azure 媒體服務 .NET 來新增編碼單位。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milangada
ms.custom: devx-track-csharp
ms.openlocfilehash: f9293b735d6a752bca30e862a09943bf21583048
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89262394"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>如何使用 .NET SDK 調整編碼

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [入口網站](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概觀
> [!IMPORTANT]
> 請務必檢閱[概觀](media-services-scale-media-processing-overview.md)，以取得調整媒體處理的詳細資訊。
> 
> 

若要使用 .NET SDK　變更保留單元類型以及編碼保留單元數目，請執行下列動作：

```csharp
IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
encodingS1ReservedUnit.Update();
Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

encodingS1ReservedUnit.CurrentReservedUnits = 2;
encodingS1ReservedUnit.Update();

Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);
```

## <a name="opening-a-support-ticket"></a>建立支援票證

依預設，每一個媒體服務帳戶可調整為最多 10 個 S2 或 S3 媒體保留單位 (MRU) 或是 25 個 S1 MRU，以及 5 個隨選串流保留單位。 您可以建立支援票證來要求更高的限制。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
