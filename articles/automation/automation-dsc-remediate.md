---
title: 修復不符合的 Azure 自動化狀態設定伺服器
description: 如何依需設定重新套用於設定狀態漂移的伺服器
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: cc5884e1f70bdccee4e7a113e6e3ee2d6604b50a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406073"
---
# <a name="remediate-noncompliant-dsc-servers"></a>修復不合規的 DSC 伺服器

當伺服器註冊到 Azure 自動化狀態設定時,設定模式`ApplyOnly`設定`ApplyandMonitor``ApplyAndAutoCorrect`為 。 如果未將模式設置為`ApplyAndAutoCorrect`,則由於任何原因從相容狀態漂移的伺服器在手動更正之前保持不合規。

Azure 計算提供了一個名為 Run 命令的功能,允許客戶在虛擬機內執行文本。
本文檔在手動更正配置漂移時提供了此功能的示例腳本。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>使用 PowerShell 正確漂移 Windows 虛擬機器

有關使用 Windows 虛擬機器上的執行命令功能的分步說明,請參閱[使用執行命令 在 Windows VM 中執行 PowerShell 文稿](/azure/virtual-machines/windows/run-command)的文件頁。

要強制 Azure 自動化狀態配置節點下載並應用它,請使用[Update-Dsc 配置](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)cmdlet。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>正確漂移 Linux 虛擬機器

類似的功能目前不適用於 Linux 伺服器。
唯一的選擇是重複註冊過程。

對於 Azure 節點,可以更正從 Azure 門戶或使用 Az 模組 cmdlet 的漂移。 有關此過程的詳細資訊記錄在[載入電腦中,供 Azure 自動化狀態設定進行管理](automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal)。
對於混合節點,您可以使用包含的 Python 文稿更正漂移。
有關[Linux 回購,請參閱 PowerShell DSC。](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)

## <a name="next-steps"></a>後續步驟

- 有關 PowerShell cmdlet 引用,請參閱[Az.自動化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 要檢視在連續部署導管中使用 Azure 自動化狀態設定的範例,請參考[Azure 自動化狀態設定進行持續部署和巧克力](automation-dsc-cd-chocolatey.md)。