---
author: baanders
description: 包含 Azure 數位 Twins 的檔案-管理實例的方法
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 887d185249f96b5d3be4aab6a96aa3c6c4a85690
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231407"
---
本文重點說明如何使用 [Azure 數位 Twins .net (c # ) **SDK**](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)來完成不同的管理作業。 您也可以使用 how [*to：使用 Azure 數位 Twins api 和 sdk*](../articles/digital-twins/how-to-use-apis-sdks.md)中所述的其他語言 sdk 來製作這些相同的管理呼叫。

> [!TIP] 
> 請記住，所有的 SDK 方法都有同步和非同步版本。 針對分頁呼叫，非同步方法會在 `AsyncPageable<T>` 同步版本傳回時傳回 `Pageable<T>` 。

另一個管理選項是直接透過 REST 用戶端（例如 Postman），呼叫此主題區域的 Azure 數位 Twins [**REST api**](/rest/api/azure-digitaltwins/) 。 如需如何執行此作業的指示，請參閱作法 [*：使用 Postman 提出要求*](../articles/digital-twins/how-to-use-postman.md)。

最後，您可以使用 Azure 數位 Twins **CLI** 來完成相同的管理作業。 若要深入瞭解如何使用 CLI，請參閱作法 [*：使用 Azure 數位 TWINS CLI*](../articles/digital-twins/how-to-use-cli.md)。