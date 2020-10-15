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
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174076"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

建立您的中樞之後，請擷取中樞的連接字串。 這會用來將裝置和應用程式連線到您的中樞。 

1. 按一下您的中樞，即可查看 IoT 中樞窗格，其中包含設定等項目。 按一下 [共用存取原則]  。
   
2. 在 [共用存取原則]  中，選取 **iothubowner** 原則。 

3. 在 [共用存取金鑰]  底下，複製 [連接字串 - 主要金鑰]  以供稍後使用。

    ![顯示如何擷取連接字串](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    如需詳細資訊，請參閱《IoT 中樞開發人員指南》中的[存取控制](../articles/iot-hub/iot-hub-devguide-security.md)。
