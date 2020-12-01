---
title: 選取和部署安全性代理程式
description: 瞭解如何在 IoT 裝置上選取和部署適用于 IoT 的 Defender 安全性代理程式。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 2634ed819b8818632c58b9e471b0f26190e5f16b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339960"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>在 IoT 裝置上選取並部署安全性代理程式

Defender for IoT 提供安全性代理程式的參考架構，可監視和收集來自 IoT 裝置的資料。
若要深入瞭解，請參閱 [安全性代理程式參考架構](security-agent-architecture.md)。

代理程式是以開放原始碼專案的形式開發，並且提供兩種類別： <br> [C](https://aka.ms/iot-security-github-c)和 [c #](https://aka.ms/iot-security-github-cs)。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 比較安全性代理程式的風格
> * 探索支援的代理程式平臺
> * 為您的解決方案選擇正確的代理程式類別

## <a name="understand-security-agent-options"></a>瞭解安全性代理程式選項

IoT 安全性代理程式的每個 Defender 類別都提供相同的功能集，並支援類似的設定選項。

以 C 為基礎的安全性代理程式具有較低的記憶體使用量，最適合使用資源較少的裝置。

|     | 以 C 為基礎的安全性代理程式 | 以 c # 為基礎的安全性代理程式 |
| --- | ----------- | --------- |
| **開放原始碼** | 在[GitHub](https://aka.ms/iot-security-github-c)中的[MIT 授權](https://en.wikipedia.org/wiki/MIT_License)下提供 | 在[GitHub](https://aka.ms/iot-security-github-cs)中的[MIT 授權](https://en.wikipedia.org/wiki/MIT_License)下提供 |
| **開發語言**    | C | C# |
| **支援的 Windows 平臺？** | 否 | 是 |
| **Windows 必要條件** | --- | [WMI](/windows/desktop/wmisdk/) |
| **支援的 Linux 平臺？** | 是、x64 和 x86 | 是，僅限 x64 |
| **Linux 必要條件** | libunwind8、libcurl3、uuid-runtime、auditd、audispd-外掛程式 | libunwind8、libcurl3、uuid-runtime、auditd、audispd-外掛程式、sudo、netstat、iptables |
| **磁片使用量** | 10.5 MB | 90 MB |
| **平均記憶體使用量 ()** | 5.5 MB | 33 MB |
| **IoT 中樞的 [驗證](concept-security-agent-authentication-methods.md)** | 是 | 是 |
| **安全性資料 [收集](how-to-agent-configuration.md#supported-security-events)** | 是 | 是 |
| **事件彙總** | 是 | 是 |
| **透過 [安全性模組](concept-security-module.md)對應項進行遠端設定** | 是 | 是 |

## <a name="security-agent-installation-guidelines"></a>安全性代理程式安裝指導方針

若為 **Windows**：安裝 SecurityAgent.ps1 腳本必須從系統管理員 PowerShell 視窗執行。

針對 **Linux**： InstallSecurityAgent.sh 必須以超級使用者身分執行。 建議您在安裝命令前面加上 "sudo"。

## <a name="choose-an-agent-flavor"></a>選擇代理程式類別

請回答下列有關 IoT 裝置的問題，以選取正確的代理程式：

- 您使用的是 _Windows Server_ 或 _windows IoT 核心_ 嗎？

    [為 Windows 部署以 c # 為基礎的安全性代理程式](how-to-deploy-windows-cs.md)。

- 您是否使用 Linux 發行版本搭配 x86 架構？

    [為 Linux 部署以 C 為基礎的安全性代理程式](how-to-deploy-linux-c.md)。

- 您是否使用 Linux 發行版本搭配 x64 架構？

    您可以使用這兩種代理程式類別。 <br>
    [為 Linux 部署以 c 為基礎的安全性代理程式](how-to-deploy-linux-c.md) 和/或 [部署適用于 Linux 的 c # 型安全性代理程式](how-to-deploy-linux-cs.md)。

這兩種代理程式類別都提供相同的功能集，並支援類似的設定選項。
若要深入瞭解，請參閱 [安全性代理程式比較](how-to-deploy-agent.md#understand-security-agent-options) 。

## <a name="supported-platforms"></a>支援的平台

下列清單包含所有目前支援的平臺。

|適用于 IoT 的 Defender 代理程式 |作業系統 |架構 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64、ARMv7|
|C|Debian 9 |    x64、x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64、ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT 核心版，組建 17763    |x64|
|

## <a name="next-steps"></a>後續步驟

若要深入瞭解設定選項，請繼續進行代理程式設定的操作指南。
> [!div class="nextstepaction"]
> [代理程式設定操作說明指南](./how-to-agent-configuration.md)