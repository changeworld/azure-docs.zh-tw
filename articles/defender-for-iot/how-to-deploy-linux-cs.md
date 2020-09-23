---
title: '安裝 & 部署 Linux c # 代理程式'
description: '瞭解如何在 Linux 上安裝和部署以 IoT c # 為基礎的安全性代理程式的 Defender'
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 48737831440a1402b6974955b4da61a4216b011f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934467"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>為適用于 Linux 的 IoT c # 型安全性代理程式部署 Defender

本指南說明如何在 Linux 上安裝和部署以 IoT c # 為基礎的安全性代理程式的 Defender。

在本指南中，您將了解如何：

> [!div class="checklist"]
> * 安裝
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解

## <a name="prerequisites"></a>必要條件

如需其他平臺和代理程式類別，請參閱 [選擇正確的安全性代理程式](how-to-deploy-agent.md)。

1. 若要部署安全性代理程式，您必須在將要用來安裝代理程式的電腦上，擁有其本機系統管理員的權限。

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝

若要部署安全性代理程式，請使用下列步驟：

1. 從 [GitHub](https://aka.ms/iot-security-github-cs)將最新版本下載到您的電腦。

1. 將套件的內容解壓縮，並流覽至 _/Install_ 資料夾。

1. 執行 `chmod +x InstallSecurityAgent.sh` 將執行權限新增至 **InstallSecurityAgent** 指令碼

1. 接下來，以 **根許可權**執行下列命令：

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   如需有關驗證參數的詳細資訊，請參閱 [如何設定驗證](concept-security-agent-authentication-methods.md)。

此指令碼會執行下列動作：

- 安裝必要條件。

- 新增服務使用者 (已停用互動式登入) 。

- 將代理程式安裝為 **Daemon** -假設裝置使用 **systemd** 進行傳統部署模型。

- 設定 **sudoers** ，以允許代理程式以 root 的形式來進行特定工作。

- 使用所提供的驗證參數來設定代理程式。

如需其他協助，請使用 –help 參數來執行指令碼：`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>解除安裝代理程式

若要解除安裝代理程式，請使用 –u 參數來執行指令碼：`./InstallSecurityAgent.sh -u`。

> [!NOTE]
> 解除安裝並不會移除安裝期間所安裝的任何遺漏必要條件。

## <a name="troubleshooting"></a>疑難排解

1. 執行下列命令來確認部署狀態：

    `systemctl status ASCIoTAgent.service`

1. 啟用記錄。
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

## <a name="next-steps"></a>下一步

- 閱讀適用于 IoT 的 Defender 服務 [總覽](overview.md)
- 深入瞭解適用于 IoT 的 Defender [架構](architecture.md)
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀 [常見問題](resources-frequently-asked-questions.md)
- 了解[警示](concept-security-alerts.md)
