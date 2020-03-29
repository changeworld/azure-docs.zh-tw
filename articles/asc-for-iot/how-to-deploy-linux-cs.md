---
title: 安裝和部署 IoT Azure 安全中心的 Linux C++ 代理指南*微軟文檔
description: 瞭解如何在 32 位和 64 位 Linux 上安裝 IoT 代理的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: b675198756ff7bc0791d49fee3649717e3e4da7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75367411"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>針對 Linux 部署適用於 IoT 的 Azure 資訊安全中心 (以 C# 為基礎) 安全性代理程式


本指南介紹了如何在 Linux 上安裝和部署基於 IoT C# 的安全代理的 Azure 安全中心。

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * 安裝
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解 

## <a name="prerequisites"></a>Prerequisites

有關其他平臺和代理風格，請參閱[選擇正確的安全代理](how-to-deploy-agent.md)。

1. 若要部署安全性代理程式，您必須在將要用來安裝代理程式的電腦上，擁有其本機系統管理員的權限。 

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝 

要部署安全代理，請使用以下步驟：

1. 從[GitHub](https://aka.ms/iot-security-github-cs)將最新版本下載到您的電腦。

1. 提取包的內容並導航到 _/安裝_資料夾。

1. 執行 `chmod +x InstallSecurityAgent.sh` 將執行權限新增至 **InstallSecurityAgent** 指令碼 

1. 接下來，使用**根許可權**運行以下命令 ： 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   有關身份驗證參數的詳細資訊，請參閱[如何配置身份驗證](concept-security-agent-authentication-methods.md)。

此指令碼會執行下列動作：

- 安裝必要條件。

- 添加服務使用者（禁用互動式登入）。

- 將代理安裝為**守護進程**- 假定設備使用**系統**用於經典部署模型。

- 配置**sudoers**以允許代理作為根執行某些任務。

- 使用所提供的驗證參數來設定代理程式。


如需其他協助，請使用 –help 參數來執行指令碼：`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>解除安裝代理程式

若要解除安裝代理程式，請使用 –u 參數來執行指令碼：`./InstallSecurityAgent.sh -u`。 

> [!NOTE]
> 解除安裝並不會移除安裝期間所安裝的任何遺漏必要條件。

## <a name="troubleshooting"></a>疑難排解  

1. 執行下列命令來確認部署狀態：

    `systemctl status ASCIoTAgent.service`

2. 啟用記錄。  
   如果代理程式無法啟動，請開啟記錄以取得詳細資訊。

   透過下列步驟開啟記錄：

   1. 在任何 Linux 編輯器中開啟要編輯的組態檔：

        `vi /var/ASCIoTAgent/General.config`

   1. 編輯下列值： 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       您可以設定 **LogFilePath** 值。 

       > [!NOTE]
       > 建議您在疑難排解完成後將記錄**關閉**。 讓記錄保持**開啟**會增加記錄檔大小和資料使用量。

   1. 執行下列命令來重新啟動代理程式：

       `systemctl restart ASCIoTAgent.service`

   1. 如需失敗的詳細資訊，請檢視記錄檔。  

       記錄檔位置是：`/var/ASCIoTAgent/IotAgentLog.log`

       根據您在步驟 2 為 **logFilePath** 選擇的名稱，來變更檔案位置路徑。 

## <a name="next-steps"></a>後續步驟

- 閱讀 Azure 安全中心，瞭解 IoT 服務[概述](overview.md)
- 瞭解有關 IoT[體系結構](architecture.md)的 Azure 安全中心
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀[常見問題解答](resources-frequently-asked-questions.md)
- 了解[警示](concept-security-alerts.md)
