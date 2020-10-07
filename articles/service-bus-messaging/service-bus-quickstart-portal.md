---
title: 使用 Azure 入口網站建立服務匯流排佇列
description: 在本快速入門中，您會了解如何使用 Azure 入口網站來建立服務匯流排命名空間和命名空間中的佇列。
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 0e5a8698da03ee4d9c7d8b0d4d59eda1be3b1883
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88185497"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>使用 Azure 入口網站建立服務匯流排命名空間和佇列
本快速入門會說明如何使用 [Azure 入口網站][Azure portal]來建立服務匯流排命名空間和佇列。 同時也會示範如何取得授權認證，讓用戶端應用程式用來將訊息傳送至佇列，或從佇列接收訊息。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>先決條件

若要完成本快速入門，請確定您具備 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以在開始前建立[免費帳戶][]。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>後續步驟
在本文中，您已建立服務匯流排命名空間和命名空間中的佇列。 若要了解如何將訊息傳送至佇列，或從佇列接收訊息，請參閱**傳送和接收訊息**一節中下列其中一個快速入門。 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues-new-package.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[免費帳戶]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
