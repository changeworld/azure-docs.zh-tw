---
title: 故障排除 Azure IoT 中心錯誤 404001 設備未找到
description: 瞭解如何修復錯誤 404001 設備未發現
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960824"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

本文介紹了**404001 設備未發現**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

在雲到設備 （C2D） 通信（如 C2D 消息、孿生更新或直接方法）期間，操作失敗，錯誤**404001 設備未發現**。

## <a name="cause"></a>原因

操作失敗，因為 IoT 中心找不到設備。 設備未註冊或禁用。

## <a name="solution"></a>解決方法

註冊您使用的裝置識別碼，然後重試。