---
title: 疑難排解 Azure IoT 中樞錯誤 404001 DeviceNotFound
description: 瞭解如何修正錯誤 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76960824"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

本文說明**404001 DeviceNotFound**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

在雲端到裝置（C2D）通訊期間（例如 C2D 訊息、對應項更新或直接方法），作業會失敗，並出現錯誤**404001 DeviceNotFound**。

## <a name="cause"></a>原因

操作失敗，因為 IoT 中樞找不到裝置。 裝置未註冊或已停用。

## <a name="solution"></a>解決方法

請註冊您所使用的裝置識別碼，然後再試一次。