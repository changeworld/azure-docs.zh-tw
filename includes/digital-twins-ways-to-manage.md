---
author: baanders
description: 包含 Azure 數位 Twins 的檔案-管理實例的方法
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533872"
---
本文重點說明如何使用 [Azure 數位 Twins .net (c # ) **SDK**](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)來完成不同的管理作業。 您也可以使用 how [*to：使用 Azure 數位 Twins api 和 sdk*](../articles/digital-twins/how-to-use-apis-sdks.md)中所述的其他語言 sdk 來製作這些相同的管理呼叫。

> [!TIP] 
> 請記住，所有的 SDK 方法都有同步和非同步版本。 針對分頁呼叫，非同步方法會在 `AsyncPageable<T>` 同步版本傳回時傳回 `Pageable<T>` 。

另一個管理選項是直接呼叫此主題區域的 Azure 數位 Twins [**REST api**](/rest/api/azure-digitaltwins/) 。

最後，您可以使用 Azure 數位 Twins **CLI** 來完成相同的管理作業。 若要深入瞭解如何使用 CLI，請參閱作法 [*：使用 Azure 數位 TWINS CLI*](../articles/digital-twins/how-to-use-cli.md)。