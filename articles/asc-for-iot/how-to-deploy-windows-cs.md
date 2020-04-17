---
title: 在 Windows 裝置上安裝 C# 代理程式
description: 瞭解如何在 32 位元或 64 位 Windows 設備上安裝 IoT 代理的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4d7d2f0a423a50f85160a856480eaa973be7e2b0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537605"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>針對 Windows 部署適用於 IoT 的 Azure 資訊安全中心 (以 C# 為基礎) 安全性代理程式

本指南介紹如何在 Windows 上安裝基於 IoT C# 的安全代理的 Azure 安全中心。

在本指南中，您將了解如何：

> [!div class="checklist"]
> * 安裝
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解

## <a name="prerequisites"></a>Prerequisites

有關其他平臺和代理風格,請參閱[選擇正確的安全代理](how-to-deploy-agent.md)。

1. 要安裝的電腦上的本地管理員許可權。

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝

要安裝安全代理,請使用以下工作流:

1. 在設備上安裝 IoT Windows C++ 代理的 Azure 安全中心。 從 IoT [GitHub 儲存庫](https://github.com/Azure/Azure-IoT-Security-Agent-CS)的 Azure 安全中心將最新版本下載到電腦。

1. 解壓縮套件的內容，然後瀏覽至 /Install 資料夾。

1. 以管理員身份打開 Windows 電源外殼。
1. 以執行以下功能向安裝安全代理文稿加入執行權限:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    然後執行:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    例如：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    關於認證參數的詳細資訊,請參考[如何設定認證](concept-security-agent-authentication-methods.md)。

此文稿執行以下操作:

* 安裝必要條件。
* 添加服務使用者(禁用互動式登錄)。
* 將代理程式安裝為**系統服務**。
* 使用所提供的驗證參數來設定代理程式。

有關其他説明,請使用 PowerShell 中的「獲取説明」 命令。

取得說明範例:```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>確認部署狀態

執行下列命令來確認代理程式的部署狀態：

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>解除安裝代理程式

若要解除安裝代理程式：

1. 執行下列 PowerShell 指令碼，其中 **-mode** 參數請設定為 **Uninstall**。

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>疑難排解

如果代理程式無法啟動，請開啟記錄 (預設會「關閉」** 記錄) 以取得詳細資訊。

若要開啟記錄：

1. 開啟設定檔 (General.config) 以使用標準檔編輯器進行編輯。

1. 編輯下列值：

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > 建議您在疑難排解完成後將記錄**關閉**。 讓記錄保持**開啟**會增加記錄檔大小和資料使用量。

1. 執行下列 PowerShell 或命令列來重新啟動代理程式：

    **電源殼**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   或

    **Cmd**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. 如需失敗的詳細資訊，請檢閱記錄檔。 紀錄檔將存在於運行文稿的工作目錄中。 

   記錄檔位置：`.\IoTAgentLog.log`

## <a name="next-steps"></a>後續步驟

* 閱讀 Azure 安全中心,瞭解 IoT 服務[概述](overview.md)
* 瞭解有關 IoT[架構結構](architecture.md)的 Azure 安全中心
* 啟用[服務](quickstart-onboard-iot-hub.md)
* 閱讀[常見問題解答](resources-frequently-asked-questions.md)
* 了解[警示](concept-security-alerts.md)
