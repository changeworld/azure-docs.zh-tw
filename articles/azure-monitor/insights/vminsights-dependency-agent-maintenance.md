---
title: 如何升級 VM 依賴項代理的 Azure 監視器
description: 本文介紹如何使用命令列、設置嚮導和其他方法升級 VM 依賴項代理的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: c55bee9880c4134f2e304a7fc5176225477fe5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480754"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>如何升級 VM 依賴項代理的 Azure 監視器

初始部署 VM 依賴項代理的 Azure 監視器後，將發佈包含錯誤修復或支援新功能或功能的更新。  本文可説明您瞭解可用的方法以及如何手動或通過自動化執行升級。

## <a name="upgrade-options"></a>升級選項 

Windows 和 Linux 的依賴項代理可以手動或自動升級到最新版本，具體取決於電腦運行的部署方案和環境。 以下方法可用於升級代理。

|環境 |安裝方法 |升級方法 |
|------------|--------------------|---------------|
|Azure VM | [適用于 Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的依賴代理 VM 擴展 | 預設情況下，代理會自動升級，除非您將 Azure 資源管理器範本配置為通過將屬性*自動升級 MinorVersion*設置為**false**來退出宣告。 禁用自動升級的次要版本的升級，以及主要版本升級遵循相同的方法 - 卸載並重新安裝擴展。 |
| 自訂 Azure VM 映射 | 手動安裝 Windows/Linux 的依賴項代理 | 需要從運行 Windows 安裝程式包或 Linux 自提取和可安裝的 shell 腳本包的命令列執行將 VM 更新到最新版本的代理。|
| 非 Azure VM | 手動安裝 Windows/Linux 的依賴項代理 | 需要從運行 Windows 安裝程式包或 Linux 自提取和可安裝的 shell 腳本包的命令列執行將 VM 更新到最新版本的代理。 |

## <a name="upgrade-windows-agent"></a>升級 Windows 代理 

要將 Windows VM 上的代理更新到未使用依賴項代理 VM 擴展程式安裝的最新版本，請從命令提示符、腳本或其他自動化解決方案運行，或者使用安裝依賴代理-Windows.exe 安裝程式嚮導運行。  

你可以[從這裡](https://aka.ms/dependencyagentwindows)下載最新版本的Windows代理。

### <a name="using-the-setup-wizard"></a>使用設置嚮導

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行**安裝獨立代理-Windows.exe**以啟動安裝程式嚮導。
   
3. 按照**依賴項代理安裝程式**嚮導卸載依賴項代理的早期版本，然後安裝最新版本。


### <a name="from-the-command-line"></a>從命令列

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行下列命令。

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    如果`/RebootMode=manual`某些進程使用以前版本中的檔並鎖定這些檔，則該參數可防止升級自動重新開機電腦。 

3. 要確認升級成功，請查看`install.log`以獲取詳細的設置資訊。 記錄目錄是 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="upgrade-linux-agent"></a>升級 Linux 代理 

支援從 Linux 上依賴項代理的早期版本進行升級，並按照與新安裝相同的命令執行。

你可以[從這裡](https://aka.ms/dependencyagentlinux)下載最新版本的Windows代理。

1. 以具有系統管理權限的帳戶登入電腦。

2. 將以下命令作為 root`sh InstallDependencyAgent-Linux64.bin -s`運行。 

如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Linux 代理上，日誌目錄是 */var/opt/microsoft/依賴代理/日誌*。 

## <a name="next-steps"></a>後續步驟

如果要停止監視 VM 一段時間或完全刪除 VM 的 Azure 監視器，請參閱[禁用 Azure 監視器中 VM 中的 VM 監視](vminsights-optout.md)。
