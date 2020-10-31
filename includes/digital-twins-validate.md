---
author: baanders
description: 包含說明如何驗證 Azure 數位 Twins 模型的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: bccf92c6c912747e28b3075f12ac1558cced30c9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130371"
---
> [!TIP]
> 建立模型之後，建議您先離線驗證模型，然後再將它們上傳至您的 Azure 數位 Twins 實例。

您可以使用與語言無關的範例來驗證模型檔，以確保 DTDL 正確，然後再將它上傳到您的實例。 它位於這裡： [**DTDL 驗證程式範例**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)。

* DTDL 驗證程式範例是以 .NET DTDL 剖析器程式庫為基礎，可在 NuGet 上作為用戶端程式庫使用： [**DigitalTwins。**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) 您也可以直接使用程式庫來設計您自己的驗證解決方案。 使用剖析器程式庫時，請務必使用與 Azure 數位 Twins 正在執行的版本相容的版本。 目前，這是 *3.12.4* 版。

您可以在 how [*to：剖析和驗證模型*](../articles/digital-twins/how-to-parse-models.md)中，深入瞭解驗證程式範例和剖析器程式庫，包括使用範例。