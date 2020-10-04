---
title: 針對 Azure Log Analytics VM 擴充功能進行疑難排解
description: 說明適用於 Windows 與 Linux Azure VM 的 Log Analytics VM 擴充功能最常見問題的徵兆、原因和解決方法。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 2e96ac5052221475d9aec11d4ed96e8f9c308d70
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710102"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>針對 Azure 監視器中的 Log Analytics VM 延伸模組進行疑難排解
本文可協助您針對在使用執行 Microsoft Azure 之 Windows 與 Linux 虛擬機器的 Log Analytics VM 擴充功能時可能遇到的錯誤進行疑難排解，並建議可能的解決方法。

若要確認擴充功能的狀態，請從 Azure 入口網站執行下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站中，按一下 [所有服務]。 在資源清單中，輸入**虛擬機器**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [虛擬機器]。
3. 在虛擬機器清單中，尋找目標虛擬機器並加以選取。
3. 在虛擬機器上，按一下 [擴充功能]****。
4. 檢查清單以查看是否已啟用 Log Analytics 擴充功能。  針對 Linux，代理程式將會被列為 **OMSAgentforLinux**。針對 Windows，代理程式將會被列為 **MicrosoftMonitoringAgent**。

   ![VM 擴充功能檢視](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. 按一下擴充功能以檢視其詳細資料。 

   ![VM 擴充功能詳細資料](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>針對 Azure Windows VM 擴充功能進行疑難排解

如果 *Microsoft Monitoring Agent* VM 擴充功能尚未安裝或沒有回報，您可以執行下列步驟來針對問題進行疑難排解。

1. 使用 [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) 中的步驟，檢查 Azure VM 代理程式是否已安裝且正確運作。
   * 您也可以檢閱 VM 代理程式記錄檔 `C:\WindowsAzure\logs\WaAppAgent.log`
   * 如果記錄檔不存在，則表示未安裝 VM 代理程式。
   * [安裝 Azure VM 代理程式](../learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 檢閱 `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent` 中的 Microsoft Monitoring Agent VM 擴充記錄檔
3. 確定虛擬機器可以執行 PowerShell 指令碼
4. 確定 C:\Windows\temp 的權限未變更
5. 在虛擬機器上提高權限的 PowerShell 視窗中輸入下列命令，以檢視 Microsoft Monitoring Agent 的狀態 `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. 檢閱 `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs` 中的 Microsoft Monitoring Agent 安裝記錄檔

如需詳細資訊，請參閱[針對 Windows 擴充功能進行疑難排解](../../virtual-machines/extensions/oms-windows.md)。

## <a name="troubleshooting-linux-vm-extension"></a>針對 Linux VM 擴充功能進行疑難排解
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
如果 *Log Analytics Linux 代理程式* VM 擴充功能尚未安裝或沒有回報，您可以執行下列步驟來針對問題進行疑難排解。

1. 如果擴充狀態是「未知」**，請檢閱 VM 代理程式記錄檔 `/var/log/waagent.log`，以檢查 Azure VM 代理程式是否已安裝且正常運作
   * 如果記錄檔不存在，則表示未安裝 VM 代理程式。
   * [在 Linux VM 上安裝 Azure VM 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux#installation)
2. 若是其他不良狀態，請檢閱 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` 和 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log` 中的 Log Analytics Linux 代理程式 VM 擴充功能記錄
3. 如果擴充狀態良好，但資料未上傳，請檢閱 `/var/opt/microsoft/omsagent/log/omsagent.log` 中的 Log Analytics Linux 代理程式記錄檔

如需詳細資訊，請參閱[針對 Linux 擴充功能進行疑難排解](../../virtual-machines/extensions/oms-linux.md)。

## <a name="next-steps"></a>後續步驟

如需與在 Azure 外部電腦上所裝載的 Log Analytics Linux 代理程式相關的其他疑難排解指引，請參閱[針對 Azure Log Analytics Linux 代理程式進行疑難排解](agent-linux-troubleshoot.md)。  

