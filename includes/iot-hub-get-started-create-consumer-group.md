---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66248933"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

[消費者組](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)在事件流中提供獨立視圖，使應用和 Azure 服務能夠獨立使用來自同一事件中心終結點的資料。 在本節中，您將消費者組添加到 IoT 中心的內置終結點，本教程稍後將用於從終結點中提取資料。

若要將取用者群組新增至 IoT 中樞，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟 IoT 中樞。

2. 在左側窗格中，選擇**內置終結點**，選擇右側窗格上的 **"事件**"，並在 **"消費者"組**下輸入名稱。 選取 [儲存]****。

   ![在 IoT 中樞中建立取用者群組](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
