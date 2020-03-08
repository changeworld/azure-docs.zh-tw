---
title: 包含檔案
description: 包含檔案
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78893105"
---
在本節中，您會使用 Azure CLI 來建立本文的裝置身分識別。 裝置識別碼會區分大小寫。

1. 開啟 [Azure Cloud Shell](https://shell.azure.com/)。

1. 在 Azure Cloud Shell 中，執行下列命令以安裝適用于 Azure CLI 的 Microsoft Azure IoT 擴充功能：

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. 建立稱為 `myDeviceId` 的新裝置身分識別，並使用下列命令來抓取裝置連接字串：

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

記下結果中的裝置連接字串。 裝置應用程式會使用此裝置連接字串，以裝置的形式連接到您的 IoT 中樞。

<!-- images and links -->
