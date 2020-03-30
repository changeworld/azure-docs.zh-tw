---
title: 故障排除 Azure IoT 中心錯誤 409001 設備已存在
description: 瞭解如何修復錯誤 409001 設備已存在
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960785"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

本文介紹了**409001 設備存在**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

嘗試在 IoT 中心註冊設備時，請求失敗，錯誤**409001 設備已存在**。

## <a name="cause"></a>原因

IoT 中心中已有具有相同裝置識別碼 的設備。 

## <a name="solution"></a>解決方法

請使用其他裝置識別碼，然後重試。