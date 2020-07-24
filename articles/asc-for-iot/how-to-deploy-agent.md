---
title: 選取和部署安全性代理程式
description: 瞭解如何為 IoT 裝置上的 IoT 安全性代理程式選取和部署 Azure 資訊安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 82103da3f2547baab62d0d95cf9a6084ef099cba
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033762"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>在 IoT 裝置上選取並部署安全性代理程式

適用于 IoT 的 Azure 資訊安全中心會提供安全性代理程式的參考架構，以監視及收集來自 IoT 裝置的資料。
若要深入瞭解，請參閱[安全性代理程式參考架構](security-agent-architecture.md)。

代理程式是以開放原始碼專案的方式開發，而且有兩種類別： <br> [C](https://aka.ms/iot-security-github-c)和[c #](https://aka.ms/iot-security-github-cs)。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 比較安全性代理程式類別
> * 探索支援的代理程式平臺
> * 為您的解決方案選擇正確的代理程式類別

## <a name="understand-security-agent-options"></a>瞭解安全性代理程式選項

IoT 安全性代理程式類別的每個 Azure 資訊安全中心都提供一組相同的功能，並支援類似的設定選項。

以 C 為基礎的安全性代理程式具有較低的記憶體使用量，而且是較少可用資源之裝置的理想選擇。

|     | 以 C 為基礎的安全性代理程式 | 以 c # 為基礎的安全性代理程式 |
| --- | ----------- | --------- |
| **開放原始碼** | 適用于[GitHub](https://aka.ms/iot-security-github-c)中的[MIT 授權](https://en.wikipedia.org/wiki/MIT_License) | 適用于[GitHub](https://aka.ms/iot-security-github-cs)中的[MIT 授權](https://en.wikipedia.org/wiki/MIT_License) |
| **開發語言**    | C | C# |
| **支援的 Windows 平臺？** | 否 | 是 |
| **Windows 必要條件** | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| **支援的 Linux 平臺？** | 是，x64 和 x86 | 是，僅限 x64 |
| **Linux 必要條件** | libunwind8、libcurl3、uuid-runtime、auditd、audispd-外掛程式 | libunwind8、libcurl3、uuid-runtime、auditd、audispd-外掛程式、sudo、netstat、iptables |
| **磁片使用量** | 10.5 MB | 90 MB |
| **記憶體使用量（平均）** | 5.5 MB | 33 MB |
| **IoT 中樞的[驗證](concept-security-agent-authentication-methods.md)** | 是 | 是 |
| **安全性資料[收集](how-to-agent-configuration.md#supported-security-events)** | 是 | 是 |
| **事件彙總** | 是 | 是 |
| **透過[安全性模組](concept-security-module.md)對應項進行遠端設定** | 是 | 是 |

## <a name="security-agent-installation-guidelines"></a>安全性代理程式安裝指導方針

針對**Windows**：安裝 SecurityAgent.ps1 腳本必須從系統管理員的 PowerShell 視窗執行。

針對**Linux**： InstallSecurityAgent.sh 必須以超級使用者身分執行。 我們建議您在安裝命令前面加上 "sudo"。

## <a name="choose-an-agent-flavor"></a>選擇代理程式類別

回答下列有關 IoT 裝置的問題，以選取正確的代理程式：

- 您使用的是_Windows Server_或_windows IoT 核心_版嗎？

    [為 Windows 部署以 c # 為基礎的安全性代理程式](how-to-deploy-windows-cs.md)。

- 您是否搭配 x86 架構使用 Linux 散發套件？

    [為 Linux 部署以 C 為基礎的安全性代理程式](how-to-deploy-linux-c.md)。

- 您是否使用 x64 架構的 Linux 散發套件？

    您可以使用這兩種代理程式類別。 <br>
    [為 Linux 部署以 c 為基礎的安全性代理程式](how-to-deploy-linux-c.md)及/或[部署適用于 Linux 的 c # 型安全性代理程式](how-to-deploy-linux-cs.md)。

這兩種代理程式種類都提供一組相同的功能，並支援類似的設定選項。
若要深入瞭解，請參閱[安全性代理程式比較](how-to-deploy-agent.md#understand-security-agent-options)。

## <a name="supported-platforms"></a>支援的平台

下列清單包含所有目前支援的平臺。

|IoT 代理程式的 Azure 資訊安全中心 |作業系統 |架構 |
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
