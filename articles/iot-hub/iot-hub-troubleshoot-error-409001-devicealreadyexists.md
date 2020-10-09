---
title: 疑難排解 Azure IoT 中樞錯誤 409001 DeviceAlreadyExists
description: 瞭解如何修正錯誤 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960785"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

本文說明 **409001 DeviceAlreadyExists** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

嘗試在 IoT 中樞註冊裝置時，要求會失敗，並出現錯誤 **409001 DeviceAlreadyExists**。

## <a name="cause"></a>原因

IoT 中樞內已經有裝置具有相同的裝置識別碼。 

## <a name="solution"></a>解決方法

請使用不同的裝置識別碼，然後再試一次。