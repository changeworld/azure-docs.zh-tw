---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648261"
---
Azure Functions 支援跨原始資源共用 (CORS)。 您可[在入口網站](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors)中，以及透過 [Azure CLI](/cli/azure/functionapp/cors) 來設定 CORS。 CORS 允許的原始來源清單適用於函數應用程式層級。 在啟用 CORS 的情況下，回應會包含 `Access-Control-Allow-Origin` 標頭。 如需詳細資訊，請參閱 [跨原始來源資源分享](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors)(英文)。 