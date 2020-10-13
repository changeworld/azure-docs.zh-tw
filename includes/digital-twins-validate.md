---
author: baanders
description: 包含說明如何驗證 Azure 數位 Twins 模型的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: fc7c31d1ab82e2e7edc74fb57233e8a0891fd113
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91974954"
---
> [!TIP]
> 建立模型之後，建議您先離線驗證模型，然後再將它們上傳至您的 Azure 數位 Twins 實例。

您可以使用與語言無關的範例來驗證模型檔，以確保 DTDL 正確無誤。 它位於這裡： [**DTDL 驗證程式範例**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)。

DTDL 驗證程式範例是以 .NET DTDL 剖析器程式庫為基礎，可在 NuGet 上作為用戶端程式庫使用： [**DigitalTwins。**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) 您也可以直接使用程式庫來設計您自己的驗證解決方案。 使用剖析器程式庫時，請務必使用與 Azure 數位 Twins 正在執行的版本相容的版本。 在預覽期間，這是 *3.12.4*版。

您可以在 how [*to：剖析和驗證模型*](../articles/digital-twins/how-to-parse-models.md)中，深入瞭解驗證程式範例和剖析器程式庫，包括使用範例。