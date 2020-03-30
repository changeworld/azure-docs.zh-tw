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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050406"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

要創建授予**服務連接**和**註冊表讀取**許可權並獲得此策略的連接字串的共用訪問策略，請按照以下步驟操作：

1. 在[Azure 門戶](https://portal.azure.com)中，選擇**資源組**。 選擇中心所在的資源組，然後從資源清單中選擇中心。

1. 在集線器的左側窗格中，選擇**共用訪問策略**。

1. 從策略清單上方的頂部功能表中，選擇 **"添加**"。

1. 在 **"添加共用訪問策略**"下，輸入策略的描述性名稱，如*服務和註冊。* 在 **"許可權**"下，選擇 **"註冊表讀取****和服務連接**"，然後選擇"**創建**"。

    ![演示如何添加新的共用訪問策略](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. 從策略清單中選擇新策略。

1. 在 **"共用便捷鍵**"下，選擇**連接字串的**複製圖示 -- 主鍵並保存該值。

    ![顯示如何擷取連接字串](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

有關 IoT 中心共用訪問策略和許可權的詳細資訊，請參閱[存取控制和許可權](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
