---
title: 排除 Azure IoT 中心錯誤 400027 連接強制關閉新連接
description: 瞭解如何修復錯誤 400027 連接強制關閉新連接
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960525"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

本文介紹了**400027 連接強制閉合新連接**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

您的設備到雲孿生操作（如讀取或修補程式報告的屬性）或直接方法調用失敗與錯誤代碼**400027**。

## <a name="cause"></a>原因

另一個用戶端使用相同的憑據創建了到 IoT 中心的新連接，因此 IoT 中心關閉了以前的連接。 IoT 中心不允許多個用戶端使用同一組憑據進行連接。

## <a name="solution"></a>解決方法

確保每個用戶端使用自己的標識連接到 IoT 中心。