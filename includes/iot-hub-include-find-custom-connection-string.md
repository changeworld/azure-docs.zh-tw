---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70050406"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

若要建立共用存取原則，其會授與**服務連線**和**登錄讀取**權限，並取得此原則的連接字串，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中選取 [資源群組]。 選取中樞所在的資源群組，然後從資源清單選取中樞。

1. 在中樞的左側窗格上，選取 [共用存取原則]。

1. 從原則清單上方的頂端功能表中，選取 [新增]。

1. 在 [新增共用存取原則] 下，輸入原則的描述性名稱，例如 *serviceAndRegistryRead*。 在 [權限] 下選取 [登錄讀取] 和 [服務連線]，然後選取 [建立]。

    ![示範如何新增共用存取原則](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. 從原則清單中選取新原則。

1. 在 [共用存取金鑰] 下，選取**連接字串 -- 主要金鑰**的複製圖示，然後儲存值。

    ![顯示如何擷取連接字串](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

如需 IoT 中樞共用存取原則和權限的詳細資訊，請參閱[存取控制及權限](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
