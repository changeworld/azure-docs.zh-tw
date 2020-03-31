---
title: Azure 安全中心的端點保護建議
description: 如何發現端點保護解決方案並將其標識為正常解決方案。
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
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208537"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure 安全中心的端點保護評估和建議

Azure 安全中心提供[受支援的](security-center-services.md#endpoint-supported)終結點保護解決方案版本的運行狀況評估。 本文介紹了導致安全中心生成以下兩項建議的方案：

* **在虛擬機器上安裝端點保護解決方案**
* **請解決機器上的 Endpoint Protection 健康情況問題**

## <a name="windows-defender"></a>Windows Defender

* 安全中心建議您在[運行獲取 MpComputer狀態](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)時 **"在虛擬機器上安裝端點保護解決方案"，** 結果為 **"啟用 AMService：False"**

* 安全中心建議您在[運行 Get-MpComputerStatus 時](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)**"解決電腦上的端點保護運行狀況問題"，** 併發生以下任一情況：

  * 以下任一屬性都是錯誤的：

    **已啟用 AM 服務**

    **啟用反間諜軟體**

    **支援即時保護**

    **行為監視器啟用**

    **Ioav 保護啟用**

    **啟用訪問保護**

  * 如果以下一個或兩個屬性為 7 或更多。

    **反間諜軟體簽名**

    **防病毒簽名**

## <a name="microsoft-system-center-endpoint-protection"></a>微軟系統中心端點保護

* 安全中心建議您在導入**SCEPMpModule（"$env：程式檔_微軟安全用戶端_MpProvider_mpProvider.psd1"）** 和運行具有**AMService 啟用的** **Get-MProt 電腦狀態**結果時 **"在虛擬機器上安裝端點保護解決方案"**

* 當**Get-Mprot 電腦狀態**運行且發生以下任一情況時，安全中心建議您 **"解決電腦上的端點保護運行狀況問題"：**

    * 以下屬性中至少有一個為 false：

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * 如果以下簽名更新中的一個或兩個大於或等於 7。 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>趨勢科技

* 當未滿足以下任何檢查時，安全中心建議您 **"在虛擬機器上安裝端點保護解決方案"：**
    * **HKLM：_軟體\趨勢微\深度安全代理**存在
    * **HKLM：_軟體\趨勢微\深度安全代理\安裝資料夾**存在
    * **dsa_query.cmd**檔位於安裝資料夾中
    * 使用元件.AM.模式運行**dsa_query.cmd**結果 **：打開 - 檢測到趨勢微深度安全代理**

## <a name="symantec-endpoint-protection"></a>賽門鐵克端點保護
當未滿足以下任何檢查時，安全中心建議您 **"在虛擬機器上安裝端點保護解決方案"：**

* **HKLM：[軟體]賽門鐵克_賽門鐵克端點保護\當前版本_PRODUCTNAME = "賽門鐵克端點保護"**

* **HKLM：[軟體]賽門鐵克_賽門鐵克端點保護\當前版本\公共-opstate_AS運行狀態 = 1**

Or

* **HKLM：[軟體]哇6432Node_賽門鐵克_賽門鐵克端點保護\當前版本_PRODUCTNAME = "賽門鐵克端點保護"**

* **HKLM：[軟體]哇6432Node_賽門鐵克_賽門鐵克端點保護\當前版本\公共-opstate_AS運行狀態 = 1**

當未滿足以下任何檢查時，安全中心建議您 **"解決機器上的端點保護運行狀況問題"：**

* 檢查賽門鐵克版本>= 12：註冊表位置 **：HKLM：\軟體\賽門鐵克_賽門鐵克端點保護\當前版本" -值"PRODUCTVERSION"**

* 檢查即時保護狀態 **：HKLM：\軟體\哇6432Node_賽門鐵克_賽門鐵克端點保護_AV_存儲_檔案系統_即時掃描\OnOff = 1**

* 檢查簽名更新狀態 **：HKLM_軟體\賽門鐵克_賽門鐵克端點保護\當前版本\公共-opstate_最新病毒Defsdate<= 7 天**

* 檢查完全掃描狀態 **：HKLM：[軟體]賽門鐵克_賽門鐵克端點保護\當前版本\公共-opstate_上次成功掃描日期時間<= 7 天**

* 查找賽門鐵克 12 的簽名版本簽名版本路徑：**註冊表路徑* "當前版本_共用 Defs" -值"SRTSP"** 

* 賽門鐵克 14 的簽名版本的路徑：**註冊表路徑* "當前版本_共用 Defs_SDSDefs" - 值"SRTSP"**

註冊表路徑：

* **"HKLM：[軟體]賽門鐵克\賽門鐵克端點保護" = $Path;**
* **"HKLM：\軟體_哇6432Node_賽門鐵克_賽門鐵克端點保護" = $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>針對 Windows 的邁克菲端點保護

當未滿足以下任何檢查時，安全中心建議您 **"在虛擬機器上安裝端點保護解決方案"：**

* **HKLM：_軟體\McAfee\端點\AV_產品版本**存在

* **HKLM：[軟體]MCAfee_AV解決方案\MCSHIELDGLOBAL_全球_全球啟用 = 1**

當未滿足以下任何檢查時，安全中心建議您 **"解決機器上的端點保護運行狀況問題"：**

* 邁克菲版本 **：HKLM：_軟體_McAfee_endpoint_AV_產品版本>= 10**

* 查找簽名版本 **：HKLM：\軟體\McAfee_AVSolution_DS_DS -值"dwContent主要版本"**

* 查找簽名日期 **：HKLM：\軟體\McAfee_AVSolution_DS_值"scontent創建日期">= 7 天**

* 查找掃描日期 **：HKLM：\軟體\McAfee_Endpoint_AV_ODS - 值"最後全掃描執行時間">= 7 天**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>用於 Linux 威脅防禦的邁克菲端點安全性 

當未滿足以下任何檢查時，安全中心建議您 **"在虛擬機器上安裝端點保護解決方案"：**

- 檔 **/選擇/isec/ens/威脅預防/箱/isecav**出口 

- **"/選擇/isec/ens/威脅預防/bin/isecav -版本"** 輸出是：**邁克菲名稱 = 用於 Linux 威脅防禦的邁克菲端點安全，以及 >的 McAfee 版本 = 10**

當未滿足以下任何檢查時，安全中心建議您 **"解決機器上的端點保護運行狀況問題"：**

- **"/選擇/isec/ens/威脅預防/bin/isecav --清單任務"** 返回**快速掃描、完整掃描**和兩個掃描<= 7 天

- **"/選擇/isec/ens/威脅預防/賓/isecav --清單任務"** 返回**DAT 和引擎更新時間**，它們<= 7 天

- **"/選擇/isec/ens/威脅預防/賓/伊塞卡夫 -getoasconfig - 摘要"** 返回**訪問掃描**狀態

## <a name="sophos-antivirus-for-linux"></a>Linux 的 Sophos 防病毒 

當未滿足以下任何檢查時，安全中心建議您 **"在虛擬機器上安裝端點保護解決方案"：**

- 檔 **/選擇/sophos-av/bin/savdstatus**退出或搜索自訂位置 **"讀取連結 $（即 savscan）"**

- **"/選擇/sophos-av/bin/savdstatus -版本"** 返回 Sophos 名稱 = **Sophos 防病毒和 Sophos 版本>= 9**

當未滿足以下任何檢查時，安全中心建議您 **"解決機器上的端點保護運行狀況問題"：**

- **"/選擇/索福斯-av/bin/slog --maxage_7 |grep-i"預定掃描"。\*已完成" |尾部 -1"**，傳回值

- **"/選擇/索福斯-av/bin/slog --maxage_7 |grep "掃描完成"** |尾部 -1"，傳回值

- **"/選擇/sophos-av/bin/savdstatus -- 上次更新"** 返回上次更新，應<= 7 天 

- **"/選擇/索福斯-av/bin/savdstatus -v"** 等於 **"正在運行訪問掃描"** 

- **"/選擇/sophos-av/bin/savconfig 獲取即時保護"** 返回啟用

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

微軟反惡意軟體擴展日誌可在： **%系統磁碟機%\WindowsAzure_Logs_Plugins_Microsoft.Azure.Security.IaaSAnti惡意軟體（或 PaaSAnti惡意軟體）\1.5.5.x（版本*）\命令執行.log**

### <a name="support"></a>支援

如需更多説明，請與[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家聯繫。 或提交 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇"獲取支援"。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。