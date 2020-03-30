---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883821"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

要獲取**註冊表 ReadWrite**策略的 IoT 中心連接字串，請按照以下步驟操作：

1. 在[Azure 門戶](https://portal.azure.com)中，選擇**資源組**。 選擇中心所在的資源組，然後從資源清單中選擇中心。

2. 在集線器的左側窗格中，選擇**共用訪問策略**。

3. 從策略清單中，選擇**註冊表ReadWrite**策略。

4. 在 **"共用便捷鍵**"下，選擇**連接字串的**複製圖示 -- 主鍵並保存該值。

    ![顯示如何擷取連接字串](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

有關 IoT 中心共用訪問策略和許可權的詳細資訊，請參閱[存取控制和許可權](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
