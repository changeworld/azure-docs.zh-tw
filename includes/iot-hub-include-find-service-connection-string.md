---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "69558488"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

要獲取**服務**策略的 IoT 中心連接字串，請按照以下步驟操作：

1. 在[Azure 門戶](https://portal.azure.com)中，選擇**資源組**。 選擇中心所在的資源組，然後從資源清單中選擇中心。

1. 在 IoT 中心的左側窗格中，選擇**共用訪問策略**。

1. 從策略清單中，選擇**服務**策略。

1. 在 **"共用便捷鍵**"下，選擇**連接字串的**複製圖示 -- 主鍵並保存該值。

    ![顯示如何擷取連接字串](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

有關 IoT 中心共用訪問策略和許可權的詳細資訊，請參閱[存取控制和許可權](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
