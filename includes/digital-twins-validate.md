---
author: baanders
description: 包含說明如何驗證 Azure 數位 Twins 模型的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 0146660fa50f9a136cd82483200ca2e6c6cc0b89
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985866"
---
> [!TIP]
> 建立模型之後，建議您先離線驗證您的模型，再將它們上傳到您的 Azure 數位 Twins 實例。

有一種與語言無關的範例可用來驗證模型檔，以確保 DTDL 正確無誤。 其位於這裡： [**DTDL 驗證程式範例**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)。

DTDL 驗證程式範例是以 .NET DTDL 剖析器程式庫為基礎，可在 NuGet 上作為用戶端程式庫：[**選取**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)。 您也可以直接使用程式庫來設計自己的驗證解決方案。 使用剖析器程式庫時，請務必使用與 Azure 數位 Twins 正在執行之版本相容的版本。 在預覽期間，這是版本*3.7.0*。

如需詳細資訊，請深入瞭解驗證程式範例和剖析器程式庫，包括使用範例，請見[*如何：剖析和驗證模型*](../articles/digital-twins/how-to-parse-models.md)。