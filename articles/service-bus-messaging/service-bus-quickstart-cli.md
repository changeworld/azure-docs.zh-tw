---
title: 快速入門 - 使用 Azure CLI 建立服務匯流排佇列 | Microsoft Docs
description: 在本快速入門中，您會了解如何使用 Azure CLI 來建立服務匯流排命名空間，以及在該命名空間中建立佇列。
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95810636"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>使用 Azure CLI 建立服務匯流排命名空間和佇列
本快速入門會說明如何使用 Azure CLI 來建立服務匯流排命名空間和佇列。 同時也會示範如何取得授權認證，讓用戶端應用程式用來將訊息傳送至佇列，或從佇列接收訊息。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>先決條件
如果您沒有 Azure 訂用帳戶，您可以在開始前建立[免費帳戶][free account]。

在本快速入門中，您會使用可在登入 Azure 入口網站後啟動的 Azure Cloud Shell。 如需 Azure Cloud Shell 的詳細資訊，請參閱 [Azure Cloud Shell 概觀](../cloud-shell/overview.md)。 您也可以[安裝](/cli/azure/install-azure-cli) Azure PowerShell 並在電腦上使用。 

## <a name="provision-resources"></a>佈建資源
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取下圖中的圖示來啟動 Azure Cloud Shell。 如果 Cloud Shell 處於 **PowerShell** 模式，請切換至 **Bash** 模式。 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="啟動 Cloud Shell":::
3. 執行下列命令以建立 Azure 資源群組。 您可以視需要更新資源群組名稱和位置。 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. 執行下列命令來建立服務匯流排傳訊命名空間。

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. 執行下列命令，在上一個步驟建立的命名空間中建立佇列。 在本範例中，`ContosoRG` 是您在上一個步驟中建立的資源群組。 `ContosoSBusNS` 是在該資源群組中建立的服務匯流排命名空間名稱。 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. 執行下列命令，以取得命名空間的主要連接字串。 您可以使用此連接字串連線到佇列，並傳送和接收訊息。 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    記下連接字串和佇列名稱。 您會使用這兩個資訊傳送及接收訊息。 


## <a name="next-steps"></a>後續步驟
在本文中，您已建立服務匯流排命名空間和命名空間中的佇列。 若要了解如何將訊息傳送至佇列，或從佇列接收訊息，請參閱 **傳送和接收訊息** 一節中下列其中一個快速入門。 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

