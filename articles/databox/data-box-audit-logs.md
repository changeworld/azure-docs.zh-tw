---
title: Azure 資料箱、Azure Data Box Heavy 事件的審核記錄 |Microsoft Docs
description: 描述在 Azure 資料箱的各個階段收集的資料箱完整審核記錄，以及 Azure Data Box Heavy 順序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209431"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure 資料箱和 Azure Data Box Heavy 的審核記錄

記錄是一段時間內發生之離散事件的不可變、有時間戳記的記錄。 記錄包含來自您裝置的診斷、審核和安全性資訊。  

資料箱或 Data Box Heavy 訂單會在其運作過程中進行下列步驟：訂購、設定、資料複製、傳回、上傳至 Azure 及驗證，以及資料清除。 針對上述每個步驟，會審核並記錄所有事件。

本文包含資料箱審核記錄的資訊，包括記錄類型和收集的資訊，以及記錄檔的位置。 

本文中的資訊適用于、Data Box 及 Data Box Heavy。 在後續的章節中，任何對資料箱的參考也適用于 Data Box Heavy。 本文不涵蓋從 Azure 中執行的資料箱服務收集的記錄。 


## <a name="about-audit-logs"></a>關於 audit 記錄 

在您的資料箱上，會收集下列記錄：

- **系統記錄** -資料箱是以 Windows 為基礎的裝置，會記錄所有的硬體、軟體和系統事件。 系統審核記錄中會收集和報告這些事件的集合。 

- **安全性** 資料箱是以 Windows 為基礎的裝置，會記錄所有安全性事件。 在安全性審核記錄中，會收集並報告這些事件的集合。 

- **應用程式** -這些記錄僅適用于資料箱。 這些記錄包含裝置上產生的所有事件，以回應執行中的資料箱服務。

下一節將討論每個記錄檔。

### <a name="system-logs"></a>系統記錄檔

下列系統記錄檔事件識別碼會收集為數據箱上的系統審核記錄：

|事件提供者名稱     |已收集事件識別碼   |事件描述   |
|-------------------|----------|----------------|
|Microsoft-Windows-核心-一般|12  |OS 重新開機的 UTC 時間。   |
|                                |13  |OS 關機的 UTC 時間。 |
|    |                              |
|Microsoft-Windows-核心-電源  |41  |系統重新開機而不需要正常關機。| 
|    |                              |
|Microsoft-Windows-BitLocker-驅動程式|全部|    |

### <a name="security-logs"></a>安全性記錄

下列安全性記錄檔事件識別碼會收集為數據箱上的安全性審核記錄：

|事件提供者名稱                   |已收集事件識別碼    |事件描述       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-Security-Auditing   |4624        |登入成功。 |
|                                      |4625        |帳戶登入失敗。 未知的使用者名稱或密碼不正確。 |
|                                     

### <a name="application-logs"></a>應用程式記錄

下列應用程式記錄檔事件識別碼會收集為數據箱上套件審核記錄的一部分。     

- **Microsoft-Azure-資料箱-OOBE-審核** -包含在本機 UI 中發生的事件。 
- **資料箱-重新布建-Audit** -包含與資料箱裝置重新布建相關的事件。 當透過本機 UI 重設裝置時，就會發生資料箱的重新布建。 當您想要清除現有的共用，並在重新布建或裝置重設過程中重新建立共用時，請選擇此選項。
- **資料箱-HcsMgmt-Audit** -包含與將裝置寄回 Azure 資料中心之前的「 **準備寄送** 」步驟相關的事件。 
- **資料箱-IfxAudit** -包含產品的不同實體所記錄的訊息，這些記錄會指出有關某些流程中所發生情況的詳細資訊。

以下資料表摘要說明各種事件提供者，以及在每個案例中收集的對應事件識別碼。

|事件提供者名稱    |事件識別碼    | 注意 |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-資料箱-OOBE-審核 |4624        |登入成功。|
|                                      |4625        |帳戶登入失敗。 未知的使用者名稱或密碼不正確。|
|                                     |4634        |登出事件。|
|                                   |  | |
|Microsoft-Azure-資料箱-重新布建-Audit    |65001       |成功的重新布建事件。|
|                                                  |65002       |無法重新布建事件。|
|                                                  |                 |         |
|Microsoft-Azure-資料箱-HcsMgmt-Audit        |65003       |寄送準備狀態事件 NotStarted、InProgress、Failed、已取消、成功、ScanCompletedWithIssues、SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-資料箱-IfxAudit    |全部 |所有事件都會以程式碼中的 audit log API 來記錄 |

以下是檢測架構 (IFX 代表) audit log 的範例：

|     工作/作業/API                              |     記錄的事件                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     清理                                   |     記錄清除作業的啟動、完成或失敗相關事件。 |                                              
|     準備裝置以供客戶運送    |     記錄啟動、完成或作業失敗的相關事件，以準備裝置以進行運送。 |
|     佈建                                 |     系統會記錄裝置布建作業的啟動、完成或失敗相關事件。|
|     Audit 封裝作業                       |     系統會記錄建立監管記錄鏈之啟動、完成或失敗的相關事件。|
|     磁片覆寫                          |     無法覆寫磁片。|
|     啟用或停用遠端 PowerShell     |     系統會記錄在裝置上啟用或停用遠端 PowerShell 的相關事件。 |
|     取得安裝階段詳細資料               |     與裝置上的軟體安裝相關的事件會記錄在 Azure 資料中心。|
|     解除鎖定或鎖定 BitLocker 磁片區           |     記錄事件以表示 *basevolume* 和 *hcsdata* 磁片區的 BitLocker 狀態。|
|     清理磁片                              |     與無法清除的實體磁片失敗相關的事件，以及在裝置上的所有實體磁片都成功清除時所發生的事件，都會記錄下來。 |
|     啟用或停用本機使用者               |     會記錄針對 >storsimpleadmin 和 PodSupportAdminUser 啟用或停用本機使用者帳戶的相關事件。| 
|     密碼重設                          |     系統會記錄與本機 >storsimpleadmin 使用者的成功或失敗密碼重設相關的事件。 |


除了 IFX 代表 audit 記錄之外，也會針對資料箱收集監管的連鎖審核記錄。 這些記錄檔無法即時查看，但只有在作業完成並從資料箱磁片中清除資料之後。 這些記錄包含 IFX 代表 audit 記錄檔中包含的資訊子集。

如需有關監管審核記錄鏈的詳細資訊，請參閱 [在資料抹除之後取得監管記錄鏈](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure)。

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>存取稽核記錄

這些記錄會儲存在 Azure 中，無法直接存取。 如果您需要存取這些記錄檔，請提出支援票證。 如需詳細資訊，請參閱 [連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)。 

一旦提出支援票證，Microsoft 將會下載並提供您這些記錄的存取權。


## <a name="next-steps"></a>接下來的步驟

- 瞭解如何 [針對資料箱和 Data Box Heavy 的問題進行疑難排解](data-box-troubleshoot.md)。
