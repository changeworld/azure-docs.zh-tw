---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "66248933"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

[取用者群組](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)可提供事件資料流的獨立檢視，讓應用程式和 Azure 服務能夠獨立取用來自相同事件中樞端點的資料。 在本節中，您會將取用者群組新增至 IoT 中樞的內建端點，稍後在本教學課程中會用來從端點提取資料。

若要將取用者群組新增至 IoT 中樞，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟 IoT 中樞。

2. 在左窗格中，選取 [ **內建端點**]，在右窗格中選取 [ **事件** ]，然後在 [取用 **者群組**] 下輸入名稱。 選取 [儲存]****。

   ![在 IoT 中樞中建立取用者群組](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
