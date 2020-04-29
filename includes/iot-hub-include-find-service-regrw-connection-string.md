---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756982"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

若要建立可授與**服務**連線和登錄**寫入**許可權的共用存取原則，以及取得此原則的連接字串，請遵循下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取 [**資源群組**]。 選取您的中樞所在的資源群組，然後從資源清單中選取您的中樞。

1. 在中樞的左側窗格中，選取 [**共用存取原則**]。

1. 從原則清單上方的頂端功能表中，選取 [**新增**]。

1. 在 [**新增共用存取原則**] 底下，輸入原則的描述性名稱，例如*serviceAndRegistryReadWrite*。 在 [**許可權**] 底下，選取 [登錄] [**寫入**和服務連線]，然後選取 [**建立** **]**。 （當您選取 [登錄**寫入**] 時，會自動包含 [登錄**讀取**] 許可權）。

    ![示範如何新增共用存取原則](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. 從原則清單中選取您的新原則。

1. 在 [**共用存取金鑰**] 底下，選取 [**連接字串-主要金鑰**] 的複製圖示，然後儲存值。

    ![顯示如何擷取連接字串](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

如需 IoT 中樞共用存取原則和許可權的詳細資訊，請參閱[存取控制和許可權](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
