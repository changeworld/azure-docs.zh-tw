---
title: Azure IoT Central 中的客戶資料常駐 |Microsoft Docs
description: 本文說明 Azure IoT Central 應用程式中的客戶資料存放區。
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280675"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central 客戶資料常駐

除了下列情況之外，IoT Central 不會將客戶資料儲存在客戶指定的地理位置：

- 將新使用者新增至現有的 IoT Central 應用程式時，使用者的電子郵件識別碼可能會儲存在地理位置以外的位置，直到受邀的使用者第一次存取應用程式為止。

- IoT Central 儀表板地圖底圖使用 [Azure 地圖服務](../../azure-maps/about-azure-maps.md)。 當您將地圖底圖新增至現有的 IoT Central 應用程式時，可能會根據 Azure 地圖服務服務的地理位置規則來處理或儲存位置資料。
