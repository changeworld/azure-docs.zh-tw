---
title: 安裝 & 部署 Linux C 代理程式
description: 瞭解如何在 Linux 上安裝和部署以 IoT C 為基礎的安全性代理程式的 Defender
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
ms.openlocfilehash: 8c03f6b882c8b1a64c9f256493c5d586b5fa0f89
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934662"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>為適用于 Linux 的 IoT C 型安全性代理程式部署 Defender

本指南說明如何在 Linux 上安裝和部署以 IoT C 為基礎的安全性代理程式的 Defender。

在本指南中，您將了解如何：

> [!div class="checklist"]
> * 安裝
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解

## <a name="prerequisites"></a>必要條件

如需其他平臺和代理程式類別，請參閱 [選擇正確的安全性代理程式](how-to-deploy-agent.md)。

1. 若要部署安全性代理程式，您想要在 (sudo) 上安裝的電腦上需要本機系統管理員許可權。

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝

若要安裝和部署安全性代理程式，請使用下列工作流程：

1. 從 [GitHub](https://aka.ms/iot-security-github-c)將最新版本下載到您的電腦。

1. 將套件的內容解壓縮，並流覽至 _/src/installation_ 資料夾。

1. 藉由執行下列命令，將執行許可權新增至 **>installsecurityagent 腳本** ：

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 接下來，請執行：

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   如需驗證參數的詳細資訊，請參閱[如何設定驗證](concept-security-agent-authentication-methods.md)。

此腳本會執行下列功能：

1. 安裝必要條件。

1. 新增服務使用者 (已停用互動式登入) 。

1. 將代理程式安裝為**精靈** - 假設裝置使用 **systemd** 來管理服務。

1. 使用所提供的驗證參數來設定代理程式。

如需其他協助，請使用 –help 參數來執行指令碼：

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>解除安裝代理程式

若要卸載代理程式，請使用–-uninstall 參數執行腳本：

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>疑難排解

執行下列命令來確認部署狀態：

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>下一步

- 閱讀適用于 IoT 的 Defender 服務 [總覽](overview.md)
- 深入瞭解適用于 IoT 的 Defender [架構](architecture.md)
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀 [常見問題](resources-frequently-asked-questions.md)
- 瞭解 [安全性警示](concept-security-alerts.md)
