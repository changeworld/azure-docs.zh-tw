---
title: 如何升級適用於 VM 的 Azure 監視器 Dependency agent
description: 本文說明如何使用命令列、安裝程式和其他方法來升級適用於 VM 的 Azure 監視器相依性代理程式。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 01dd8422658aa0c8982733e48782efd27c1bf5be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81617851"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>如何升級適用於 VM 的 Azure 監視器 Dependency agent

初始部署適用於 VM 的 Azure 監視器相依性代理程式之後，會發行包含 bug 修正或支援新功能的更新。  本文可協助您瞭解可用的方法，以及如何手動或透過自動化來執行升級。

## <a name="upgrade-options"></a>升級選項 

您可以手動或自動將適用于 Windows 和 Linux 的相依性代理程式升級為最新版本，這取決於電腦執行所在的部署案例和環境。 您可以使用下列方法來升級代理程式。

|環境 |安裝方法 |升級方法 |
|------------|--------------------|---------------|
|Azure VM | 適用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的相依性代理程式 VM 擴充功能 | 代理程式預設會自動升級，除非您將 Azure Resource Manager 範本 *設定為 [* **false**]，以退出宣告。 已停用自動升級的次要版本升級，而主要版本升級會遵循相同的方法-卸載並重新安裝擴充功能。 |
| 自訂 Azure VM 映射 | 手動安裝 Windows/Linux 的相依性代理程式 | 將 Vm 更新至最新版本的代理程式時，必須從執行 Windows installer 套件或 Linux 自動解壓縮和可安裝之 shell 腳本組合的命令列執行。|
| 非 Azure Vm | 手動安裝 Windows/Linux 的相依性代理程式 | 將 Vm 更新至最新版本的代理程式時，必須從執行 Windows installer 套件或 Linux 自動解壓縮和可安裝之 shell 腳本組合的命令列執行。 |

## <a name="upgrade-windows-agent"></a>升級 Windows 代理程式 

若要將 Windows VM 上的代理程式更新為未使用相依性代理程式 VM 擴充功能安裝的最新版本，您可以從命令提示字元、腳本或其他自動化解決方案執行，或使用 InstallDependencyAgent-Windows.exe 安裝精靈。  

您可以從 [這裡](https://aka.ms/dependencyagentwindows)下載最新版本的 Windows 代理程式。

### <a name="using-the-setup-wizard"></a>使用設定向導

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行 **InstallDependencyAgent-Windows.exe** 以啟動安裝精靈。
   
3. 依照 **Dependency Agent 設定** 嚮導卸載舊版的相依性代理程式，然後安裝最新版本。


### <a name="from-the-command-line"></a>從命令列

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行下列命令。

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    `/RebootMode=manual`如果某些處理常式使用舊版的檔案，並擁有鎖定，此參數可防止升級自動重新開機電腦。 

3. 若要確認升級是否成功，請檢查 `install.log` 以取得詳細的安裝資訊。 記錄目錄是 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="upgrade-linux-agent"></a>升級 Linux 代理程式 

支援從舊版的相依性代理程式升級，並使用與新安裝相同的命令來執行。

您可以從 [這裡](https://aka.ms/dependencyagentlinux)下載最新版的 Linux 代理程式。

1. 以具有系統管理權限的帳戶登入電腦。

2. 以 root 身份執行下列命令 `sh InstallDependencyAgent-Linux64.bin -s` 。 

如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Linux 代理程式上，記錄檔目錄是 */var/opt/microsoft/dependency-agent/log*。 

## <a name="next-steps"></a>接下來的步驟

如果您想要在一段時間內停止監視 Vm，或完全移除適用於 VM 的 Azure 監視器，請參閱 [適用於 VM 的 Azure 監視器中停用 vm 的監視](vminsights-optout.md)功能。
