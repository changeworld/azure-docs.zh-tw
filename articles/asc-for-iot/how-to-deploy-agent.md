---
title: 為 IoT 代理選擇和部署 Azure 安全中心*微軟文檔
description: 瞭解如何在 IoT 設備上為 IoT 安全代理選擇和部署 Azure 安全中心。
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
ms.openlocfilehash: d70f2f3ec87c8673013bcf7b6f70ebcbb8d06f08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770011"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>在 IoT 設備上選擇並部署安全代理

IoT Azure 安全中心為監視和從 IoT 設備收集資料的安全代理提供了參考體系結構。
要瞭解更多資訊，請參閱[安全代理參考體系結構](security-agent-architecture.md)。

代理開發為開源專案，有兩種類型： <br> [C](https://aka.ms/iot-security-github-c)和[C#](https://aka.ms/iot-security-github-cs)。

在本文中，您將學會如何： 
> [!div class="checklist"]
> * 比較安全代理風格
> * 發現支援的代理平臺
> * 為您的解決方案選擇合適的代理風格

## <a name="understand-security-agent-options"></a>瞭解安全代理選項

每個適用于 IoT 安全代理風格的 Azure 安全中心都提供相同的功能集，並支援類似的配置選項。 

基於 C 的安全代理的記憶體佔用量較低，是可用資源較少的設備的理想選擇。 

|     | 基於 C 的安全代理 | 基於 C# 的安全代理 |
| --- | ----------- | --------- |
| 開源 | 在[GitHub](https://aka.ms/iot-security-github-cs)中的[MIT 許可證](https://en.wikipedia.org/wiki/MIT_License)下提供 | 在[GitHub](https://aka.ms/iot-security-github-c)中的[MIT 許可證](https://en.wikipedia.org/wiki/MIT_License)下提供 |
| 開發語言    | C | C# |
| 支援 Windows 平臺？ | 否 | 是 |
| 視窗先決條件 | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| 支援的 Linux 平臺？ | 是，x64 和 x86 | 是，僅限 x64 |
| Linux 必要條件 | libunwind8， libcurl3， uuid-運行時， 審核， 奧迪德-外掛程式 | libunwind8， libcurl3， uuid-運行時， 審核， 奧迪斯pd-外掛程式， sudo， netstat， iptables |
| 磁片佔用空間 | 10.5 MB | 90 MB |
| 記憶體佔用量（平均） | 5.5 MB | 33 MB |
| [對](concept-security-agent-authentication-methods.md)IoT 中心的身份驗證 | 是 | 是 |
| 安全[資料收集](how-to-agent-configuration.md#supported-security-events) | 是 | 是 |
| 事件彙總 | 是 | 是 |
| 通過[安全模組孿生](concept-security-module.md)遠端配置 | 是 | 是 |
|

## <a name="security-agent-installation-guidelines"></a>安全代理安裝指南

對於**Windows**：安裝安全代理.ps1 腳本必須從管理員 PowerShell 視窗執行。 

對於**Linux：InstallSecurityAgent.sh**必須以超級使用者身份運行。 我們建議將安裝命令的首碼為"sudo"。


## <a name="choose-an-agent-flavor"></a>選擇代理風味 

回答有關 IoT 設備的以下問題，以選擇正確的代理：

- 是使用_Windows 伺服器_還是_Windows IoT 核心_？ 

    [為 Windows 部署基於 C# 的安全代理](how-to-deploy-windows-cs.md)。

- 您是否使用帶有 x86 體系結構的 Linux 發行版本？ 

    [為 Linux 部署基於 C 的安全代理](how-to-deploy-linux-c.md)。

- 您是否使用帶有 x64 體系結構的 Linux 發行版本？

    可以使用兩種代理口味。 <br>
    [為 Linux 部署基於 C 的安全代理](how-to-deploy-linux-c.md)和/或[為 Linux 部署基於 C# 的安全代理](how-to-deploy-linux-cs.md)。

兩種代理類型都提供相同的功能集，並支援類似的配置選項。
請參閱[安全代理比較](how-to-deploy-agent.md#understand-security-agent-options)以瞭解更多資訊。

## <a name="supported-platforms"></a>支援的平台

以下清單包括當前支援的所有平臺。

|IoT 代理的 Azure 安全中心 |作業系統 |架構 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64， ARMv7|
|C|Debian 9 |   x64、x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64， ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT 酷睿，生成 17763    |x64|
|

## <a name="next-steps"></a>後續步驟

要瞭解有關配置選項的詳細資訊，請繼續執行代理配置操作指南。 
> [!div class="nextstepaction"]
> [如何指導代理配置](./how-to-agent-configuration.md)
