---
author: baanders
description: 包含說明如何驗證 Azure 數位 Twins 模型的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026059"
---
> [!TIP]
> 建立模型之後，建議您先離線驗證模型，然後再將它們上傳至您的 Azure 數位 Twins 實例。

您可以使用與語言無關的範例來驗證模型檔，以確保 DTDL 正確，然後再將它上傳到您的實例。 它位於這裡： [**DTDL 驗證程式範例**](/samples/azure-samples/dtdl-validator/dtdl-validator)。

* DTDL 驗證程式範例是以 .NET DTDL 剖析器程式庫為基礎，可在 NuGet 上作為用戶端程式庫使用： [**DigitalTwins。**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) 您也可以直接使用程式庫來設計您自己的驗證解決方案。 使用剖析器程式庫時，請務必使用與 Azure 數位 Twins 正在執行的版本相容的版本。 目前，這是 *3.12.4* 版。

您可以在 how [*to：剖析和驗證模型*](../articles/digital-twins/how-to-parse-models.md)中，深入瞭解驗證程式範例和剖析器程式庫，包括使用範例。