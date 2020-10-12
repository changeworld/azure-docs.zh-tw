---
title: Azure 安全性中心的端點保護建議
description: 如何探索和識別 endpoint protection 解決方案的狀況良好。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 7a9541eb3b7c662b43de0d3a609ecec4fe2621ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86519400"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure 資訊安全中心中的 Endpoint protection 評定和建議

Azure 資訊安全中心提供 [支援](security-center-services.md#endpoint-supported) 的 Endpoint protection 解決方案版本的健康情況評量。 本文說明潛在客戶安全性中心產生下列兩項建議的案例：

* **在您的虛擬機器上安裝 endpoint protection 解決方案**
* **請解決機器上的 Endpoint Protection 健康情況問題**

## <a name="windows-defender"></a>Windows Defender

* 資訊安全中心會建議您在[MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)執行時「**在虛擬機器上安裝 endpoint protection 解決方案**」，且結果為**AMServiceEnabled： False**

* 當[MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)執行時，資訊安全中心會建議您「**解決電腦上的端點保護健康情況問題**」，並進行下列任何一項動作：

  * 下列任何一個屬性為 false：

    **AMServiceEnabled**

    **AntispywareEnabled**

    **RealTimeProtectionEnabled**

    **BehaviorMonitorEnabled**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * 如果下列其中一或兩個屬性為7或以上。

    **AntispywareSignatureAge**

    **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* 資訊安全中心建議您在匯入**SCEPMpModule ( 「$env:P Rogramfiles\microsoft 安全性 Client\MpProvider\MpProvider.psd1** 」時，「**在虛擬機器上安裝 endpoint protection 解決方案**」 ) 並執行**AMServiceEnabled = false**的**Get MProtComputerStatus**結果

* 當**MprotComputerStatus**執行時，資訊安全中心會建議您「**解決電腦上的端點保護健康情況問題**」，並進行下列任何一項動作：

  * 至少下列其中一個屬性為 false：

    * **AMServiceEnabled**
    * **AntispywareEnabled**
    * **RealTimeProtectionEnabled**
    * **BehaviorMonitorEnabled**
    * **IoavProtectionEnabled**
    * **OnAccessProtectionEnabled**

  * 如果下列其中一個或兩個簽章更新大於或等於7。 

    * **AntispywareSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>趨勢科技

* 當下列任何一項檢查不符合時，資訊安全中心會建議您「 **在虛擬機器上安裝 endpoint protection 解決方案** 」：
    * **HKLM： \ SOFTWARE\TrendMicro\Deep 安全性代理程式** 存在
    * **HKLM： \ SOFTWARE\TrendMicro\Deep 安全性 Agent\InstallationFolder** 存在
    * 您可以在安裝資料夾中找到 **dsa_query .cmd** 檔
    * 正在執行 **dsa_query .cmd 的 .cmd** 結果。 **模式：偵測到趨勢微的深度安全性代理程式**

## <a name="symantec-endpoint-protection"></a>Symantec endpoint protection
當下列任何一項檢查不符合時，資訊安全中心會建議您「 **在虛擬機器上安裝 endpoint protection 解決方案** 」：

* **HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

或者

* **HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

當下列任何一項檢查不符合時，資訊安全中心會建議您「 **解決電腦上的端點保護健康情況問題** 」：

* 檢查 Symantec 版本 >= 12：登錄位置： **HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-Value" PRODUCTVERSION "**

* 檢查即時保護狀態： **HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**

* 檢查簽章更新狀態： **HKLM\Software\Symantec\Symantec 端點 Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 天**

* 檢查完整掃描狀態： **HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 天**

* 尋找 Symantec 12 的簽章版本的簽章版本號碼路徑：登錄 **路徑 + "CurrentVersion\SharedDefs"-值 "SRTSP"** 

* Symantec 14 的簽章版本路徑：登錄 **路徑 + "CurrentVersion\SharedDefs\SDSDefs"-值 "SRTSP"**

登錄路徑：

* **"HKLM： \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>適用于 Windows 的 McAfee endpoint protection

當下列任何一項檢查不符合時，資訊安全中心會建議您「 **在虛擬機器上安裝 endpoint protection 解決方案** 」：

* **HKLM： \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** 存在

* **HKLM： \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

當下列任何一項檢查不符合時，資訊安全中心會建議您「 **解決電腦上的端點保護健康情況問題** 」：

* McAfee 版本： **HKLM： \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* 尋找簽名版本： **HKLM： \ Software\McAfee\AVSolution\DS\DS-Value "dwContentMajorVersion"**

* 尋找簽名日期： **HKLM： \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" >= 7 天**

* 尋找掃描日期： **HKLM： \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" >= 7 天**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>適用于 Linux 的 McAfee 端點安全性威脅防護 

當下列任何一項檢查不符合時，資訊安全中心會建議您「 **在虛擬機器上安裝 endpoint protection 解決方案** 」：

- File **/opt/isec/ens/threatprevention/bin/isecav** 結束 

- **"/opt/isec/ens/threatprevention/bin/isecav--version"** 輸出為： **mcafee name = 適用于 Linux 的 Mcafee Endpoint Security 威脅防護和 mcafee 版本 >= 10**

當下列任何一項檢查不符合時，資訊安全中心會建議您「 **解決電腦上的端點保護健康情況問題** 」：

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 會傳回 **快速掃描、完整掃描** ，以及兩個掃描 <= 7 天

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 會傳回 **DAT 和引擎更新時間** ，而且兩者都 <= 7 天

- 「 **/opt/isec/ens/threatprevention/bin/isecav--getoasconfig--summary** 」會**在存取掃描**狀態傳回

## <a name="sophos-antivirus-for-linux"></a>Sophos 適用于 Linux 的防毒軟體 

當下列任何一項檢查不符合時，資訊安全中心會建議您「 **在虛擬機器上安裝 endpoint protection 解決方案** 」：

- 檔案 **/opt/sophos-av/bin/savdstatus** 會結束或搜尋自訂位置 **"readlink $ (savscan) "**

- **"/opt/sophos-av/bin/savdstatus--version"** 會傳回 sophos Name = **Sophos 防毒軟體和 sophos 版本 >= 9**

當下列任何一項檢查不符合時，資訊安全中心會建議您「 **解決電腦上的端點保護健康情況問題** 」：

- **"/opt/sophos-av/bin/savlog--maxage = 7 |grep-i "排程的掃描。 \* 已完成」 |tail-1 "**，傳回值

- **"/opt/sophos-av/bin/savlog--maxage = 7 |grep 「掃描已完成** 」 |tail-1 "，傳回值

- **"/opt/sophos-av/bin/savdstatus--lastupdate"** 會傳回 lastupdate，這應該是 <= 7 天 

- **"/opt/sophos-av/bin/savdstatus-v"** 等於 **"存取掃描正在執行中"** 

- 「 **/opt/sophos-av/bin/savconfig Get LiveProtection** 」傳回已啟用

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

您可以在下列位置找到 Microsoft Antimalware 擴充記錄檔： **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (或 PaaSAntimalware) \1.5.5.x (版本 # ) \commandexecution.log**

### <a name="support"></a>支援

如需更多協助，請洽詢 [MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 azure 專家。 或提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。