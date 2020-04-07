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
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756982"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

要建立授予**服務連線**和**註冊表寫入**權限的分享存取策略,並取得此策略的連接字串,請按照以下步驟操作:

1. 在[Azure 門戶](https://portal.azure.com)中,選擇**資源群組**。 選擇中心所在的資源組,然後從資源清單中選擇中心。

1. 在集線器的左方窗格中,選擇**分享存取原則**。

1. 從策略清單上方的頂部功能表中,選擇 **「添加**」。

1. 在 **'新增分享存取策略**'下,輸入策略的描述性名稱,如*服務和註冊讀寫*。 在 **「權限**」下,選擇**註冊表寫入****和服務連接**,然後選擇"**創建**" 。 (當您選擇**註冊表寫入**時,**註冊表讀取**許可權將自動包含在內。

    ![簡報如何新增分享的存取原則](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. 從策略清單中選擇新策略。

1. 在 **"共用存取鍵**"下,選擇**連接字串的**複製圖示 -- 主鍵並保存該值。

    ![顯示如何擷取連接字串](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

有關 IoT 中心分享存取策略和權限的詳細資訊,請參閱[存取控制和權限](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
