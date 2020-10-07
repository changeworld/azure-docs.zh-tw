---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "72329627"
---
Azure Functions 可讓您直接將 Azure 服務和其他資源連線至函式，而不需要自行撰寫整合程式碼。 這些*繫結*同時代表輸入和輸出，會宣告於函式定義內。 繫結中的資料會提供給函式作為參數。 「觸發程序」  是一種特殊的輸入繫結。 雖然函式只有一個觸發程序，但可以有多個輸入和輸出繫結。 若要深入了解，請參閱 [Azure Functions 觸發程序和繫結概念](../articles/azure-functions/functions-triggers-bindings.md)。