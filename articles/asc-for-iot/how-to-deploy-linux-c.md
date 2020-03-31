---
title: 安裝和部署 IoT 代理 Azure 安全中心的 Linux C 代理指南*微軟文檔
description: 瞭解如何在 32 位和 64 位 Linux 上安裝 IoT 代理的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7578811c37cd0bbe47821dadacce5fa5974f56cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68812723"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>針對 Linux 部署適用於 IoT 的 Azure 資訊安全中心 (以 C 為基礎) 安全性代理程式

本指南介紹了如何在 Linux 上安裝和部署基於 IoT C 的安全代理的 Azure 安全中心。

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * 安裝
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解 

## <a name="prerequisites"></a>Prerequisites

有關其他平臺和代理風格，請參閱[選擇正確的安全代理](how-to-deploy-agent.md)。

1. 要部署安全代理，需要在要在 （sudo） 上安裝的電腦上使用本地管理員許可權。

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝 

要安裝和部署安全代理，請使用以下工作流：


1. 從[GitHub](https://aka.ms/iot-security-github-c)將最新版本下載到您的電腦。

1. 提取包的內容並導航到 _/src/安裝_資料夾。

1. 通過運行以下命令向**安裝安全代理腳本**添加運行許可權：
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 接下來，請執行： 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   如需驗證參數的詳細資訊，請參閱[如何設定驗證](concept-security-agent-authentication-methods.md)。

此腳本執行以下功能：

1. 安裝必要條件。

2. 添加服務使用者（禁用互動式登入）。

3. 將代理程式安裝為**精靈** - 假設裝置使用 **systemd** 來管理服務。

4. 使用所提供的驗證參數來設定代理程式。 

如需其他協助，請使用 –help 參數來執行指令碼： 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>解除安裝代理程式

要卸載代理，請使用 _-卸載參數運行腳本：

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>疑難排解
執行下列命令來確認部署狀態：

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>後續步驟
- 閱讀 Azure 安全中心，瞭解 IoT 服務[概述](overview.md)
- 瞭解有關 IoT[體系結構](architecture.md)的 Azure 安全中心
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀[常見問題解答](resources-frequently-asked-questions.md)
- 瞭解[安全警報](concept-security-alerts.md)
