---
title: 如何升級適用於 VM 的 Azure 監視器 Dependency agent |Microsoft Docs
description: 本文說明如何使用命令列、安裝程式和其他方法來升級適用於 VM 的 Azure 監視器 Dependency agent。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: c98c48a4494ac37ef4868c44d4a7adacfd0d48da
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662428"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>如何升級適用於 VM 的 Azure 監視器 Dependency agent

初始部署適用於 VM 的 Azure 監視器 Dependency agent 之後，會發行更新，其中包括 bug 修正或新功能或功能的支援。  這篇文章可協助您瞭解可用的方法，以及如何以手動方式或透過自動化來執行升級。

## <a name="upgrade-options"></a>升級選項 

Windows 和 Linux 的相依性代理程式可以手動或自動升級為最新版本，端視機器執行所在的部署案例和環境而定。 下列方法可用於升級代理程式。

|環境 |安裝方式 |升級方法 |
|------------|--------------------|---------------|
|Azure VM | 適用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的相依性代理程式 VM 擴充功能 | 除非您已將 Azure Resource Manager 範本*設定為* **false**，否則預設會自動升級 Agent。 已停用自動升級的次要版本升級，且主要版本升級會遵循相同的方法-卸載並重新安裝延伸模組。 |
| 自訂 Azure VM 映射 | 手動安裝適用于 Windows/Linux 的 Dependency agent | 若要將 Vm 更新至最新版本的代理程式，您必須從執行 Windows installer 套件或 Linux 自我解壓縮和可安裝的 shell 腳本組合的命令列執行。|
| 非 Azure Vm | 手動安裝適用于 Windows/Linux 的 Dependency agent | 若要將 Vm 更新至最新版本的代理程式，您必須從執行 Windows installer 套件或 Linux 自我解壓縮和可安裝的 shell 腳本組合的命令列執行。 |

## <a name="upgrade-windows-agent"></a>升級 Windows 代理程式 

若要將 Windows VM 上的代理程式更新為不是使用相依性代理程式 VM 延伸模組安裝的最新版本，您可以從命令提示字元、腳本或其他自動化解決方案，或使用可以 installdependencyagent-windows.exe 安裝精靈來執行。  

您可以從[這裡](https://aka.ms/dependencyagentwindows)下載最新版本的 Windows 代理程式。

### <a name="using-the-setup-wizard"></a>使用安裝程式嚮導

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行**可以 installdependencyagent-windows.exe**啟動安裝精靈。
   
3. 依照**Dependency Agent 安裝程式**嚮導卸載舊版的相依性代理程式，然後再安裝最新版本。


### <a name="from-the-command-line"></a>從命令列

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行下列命令。

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    如果某些進程使用舊版的檔案，並有鎖定，則 `/RebootMode=manual` 參數可防止升級自動重新開機電腦。 

3. 若要確認升級是否成功，請檢查 `install.log` 以取得詳細的安裝資訊。 記錄目錄是 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="upgrade-linux-agent"></a>升級 Linux 代理程式 

支援在 Linux 上從舊版相依性代理程式升級，並遵循與新安裝相同的命令來執行。

您可以從[這裡](https://aka.ms/dependencyagentlinux)下載最新版本的 Windows 代理程式。

1. 以具有系統管理權限的帳戶登入電腦。

2. 以 root`sh InstallDependencyAgent-Linux64.bin -s`的身分執行下列命令。 

如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Linux 代理程式上，記錄檔的目錄是 /var/opt/microsoft/dependency-agent/log。 

## <a name="next-steps"></a>後續步驟

如果您想要在一段時間內停止監視 Vm，或完全移除適用於 VM 的 Azure 監視器，請參閱[在適用於 VM 的 Azure 監視器中停用 vm 的監視](vminsights-optout.md)。
