---
title: 在入口網站中建立 Azure Functions Premium 方案
description: 瞭解如何使用 Azure 入口網站來建立在 Premium 方案中執行的函數應用程式。
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 20921423247dda3cbb39b58dcc805dac6d367390
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937599"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>在 Azure 入口網站中建立高階方案函數應用程式

Azure Functions 提供可擴充的高階方案，可提供虛擬網路連線、無冷啟動，以及 Premium 硬體。 若要深入瞭解，請參閱 [Azure Functions Premium 方案](functions-premium-plan.md)。 

在本文中，您將瞭解如何使用 Azure 入口網站在高階方案中建立函數應用程式。 

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

## <a name="create-a-function-app"></a>建立函數應用程式

您必須擁有函式應用程式以便主控函式的執行。 函式應用程式可讓您將多個函式群組為邏輯單位，以方便您管理、部署、調整和共用資源。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

此時，您可以在新的函數應用程式中建立函式。 這些函數可利用 [Premium 方案](functions-premium-plan.md)的優點。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增由 HTTP 觸發的函式](functions-create-first-azure-function.md#create-function)
