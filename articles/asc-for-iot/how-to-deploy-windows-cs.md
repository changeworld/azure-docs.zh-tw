---
title: '在 Windows 裝置上安裝 c # 代理程式'
description: 瞭解如何在32位或64位的 Windows 裝置上安裝適用于 IoT 代理程式的 Azure 資訊安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 64b9176a16f4d8d2b7ee7f33e00714d811c5f070
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209090"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>針對 Windows 部署適用於 IoT 的 Azure 資訊安全中心 (以 C# 為基礎) 安全性代理程式

本指南說明如何在 Windows 上安裝適用于 IoT c # 型安全性代理程式的 Azure 資訊安全中心。

在本指南中，您將了解如何：

> [!div class="checklist"]
> * 安裝
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解

## <a name="prerequisites"></a>必要條件

如需其他平臺和代理程式類別，請參閱 [選擇正確的安全性代理程式](how-to-deploy-agent.md)。

1. 您想要安裝的電腦上的本機系統管理員許可權。

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝

若要安裝安全性代理程式，請使用下列工作流程：

1. 在裝置上安裝適用于 IoT Windows c # 代理程式的 Azure 資訊安全中心。 從適用于 IoT [GitHub 存放庫](https://github.com/Azure/Azure-IoT-Security-Agent-CS)的 Azure 資訊安全中心，將最新版本下載至您的電腦。

1. 解壓縮套件的內容，然後瀏覽至 /Install 資料夾。

1. 以系統管理員身分開啟 Windows PowerShell。
1. 執行下列程式碼，以將執行中的許可權新增至 InstallSecurityAgent 腳本：

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    然後執行：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    例如：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    如需驗證參數的詳細資訊，請參閱 [如何設定驗證](concept-security-agent-authentication-methods.md)。

此腳本會執行下列動作：

* 安裝必要條件。
* 新增已停用) 互動式登入的服務使用者 (。
* 將代理程式安裝為**系統服務**。
* 使用所提供的驗證參數來設定代理程式。

如需其他協助，請在 PowerShell 中使用 Get-help 命令。

Get-help 範例：    ```Get-Help .\InstallSecurityAgent.ps1```

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

1. 使用標準的檔案編輯器，開啟設定檔案 ( # A0) 進行編輯。

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

    **Powershell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   或

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. 如需失敗的詳細資訊，請檢閱記錄檔。 記錄檔會出現在我們執行腳本的工作目錄中。 

   記錄檔位置：`.\IoTAgentLog.log`

## <a name="next-steps"></a>後續步驟

* 閱讀 IoT 服務的 Azure 資訊安全中心 [總覽](overview.md)
* 深入瞭解 IoT[架構](architecture.md)的 Azure 資訊安全中心
* 啟用[服務](quickstart-onboard-iot-hub.md)
* 閱讀 [常見問題](resources-frequently-asked-questions.md)
* 了解[警示](concept-security-alerts.md)
