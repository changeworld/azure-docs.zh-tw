---
title: 安裝 & 部署 Linux C 代理程式
description: 瞭解如何在32位和64位的 Linux 上安裝適用于 IoT 代理程式的 Azure 資訊安全中心。
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
ms.openlocfilehash: d9f9602a19a266c70b17422e90566f72de2978f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311197"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>針對 Linux 部署適用於 IoT 的 Azure 資訊安全中心 (以 C 為基礎) 安全性代理程式

本指南說明如何在 Linux 上安裝和部署以 IoT C 為基礎之安全性代理程式的 Azure 資訊安全中心。

在本指南中，您將了解如何：

> [!div class="checklist"]
> * 安裝
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解

## <a name="prerequisites"></a>先決條件

如需其他平臺和代理程式類別，請參閱[選擇正確的安全性代理程式](how-to-deploy-agent.md)。

1. 若要部署安全性代理程式，您想要安裝的電腦上需要本機系統管理員許可權（sudo）。

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝

若要安裝及部署安全性代理程式，請使用下列工作流程：

1. 從[GitHub](https://aka.ms/iot-security-github-c)下載最新版本到您的電腦。

1. 將套件的內容解壓縮，並流覽至 _/src/installation_資料夾。

1. 執行下列命令，將執行中的許可權新增至**InstallSecurityAgent 腳本**：

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

1. 新增服務使用者（已停用互動式登入）。

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

## <a name="next-steps"></a>後續步驟

- 閱讀 IoT 服務的 Azure 資訊安全中心[總覽](overview.md)
- 深入瞭解 IoT[架構](architecture.md)的 Azure 資訊安全中心
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀[常見問題](resources-frequently-asked-questions.md)
- 瞭解[安全性警示](concept-security-alerts.md)
