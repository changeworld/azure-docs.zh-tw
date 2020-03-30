---
title: 包含檔案
description: 包含檔案
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70049067"
---
<!-- put the ## header in the file that includes this file -->

在本節中，您會在 IoT 中樞的身分識別登錄中建立裝置身分識別。 除非設備在標識註冊表中具有條目，否則無法連接到集線器。 有關詳細資訊，請參閱[IoT 中心開發人員指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations)。

1. 在 IoT 中心導航功能表中，打開**IoT 設備**，然後選擇 **"新建"** 以在 IoT 中心中添加設備。

    ![在入口網站中建立裝置識別](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. 在 **"創建設備"中**，為新設備（如**myDeviceId）** 提供名稱，然後選擇 **"保存**"。 此操作會為 IoT 中心創建設備標識。

   ![新增裝置](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. 建立裝置之後，請在 [IoT 裝置]**** 窗格中的清單開啟裝置。 複製**主連接字串**以以後使用。

    ![裝置連接字串](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對 IoT 中樞的安全存取。 它會儲存裝置識別碼和金鑰來作為安全性認證，以及啟用或停用旗標，讓您用來停用個別裝置的存取。 如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。
