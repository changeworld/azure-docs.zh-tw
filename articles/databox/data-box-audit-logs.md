---
title: Azure 資料箱，Azure Data Box Heavy 事件的 Audit 記錄 |Microsoft Docs
description: 描述在 Azure 資料箱的各個階段收集的資料箱完整的審核記錄，以及 Azure Data Box Heavy 順序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209431"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure 資料箱和 Azure Data Box Heavy 的審核記錄

記錄是不變的，會在一段時間內發生的離散事件加上時間戳記。 記錄包含來自您裝置的診斷、audit 和安全性資訊。  

資料箱或 Data Box Heavy 順序會在其作業過程中進行下列步驟：訂單、設定、資料複製、傳回、上傳至 Azure 並進行驗證，以及資料抹除。 針對上述每個步驟，會審核並記錄所有事件。

本文包含資料箱 audit 記錄的資訊，包括記錄類型和收集的資訊，以及記錄檔的位置。 

本文中的資訊適用于、資料箱和 Data Box Heavy。 在後續章節中，資料箱的任何參考也適用于 Data Box Heavy。 本文未涵蓋從 Azure 中執行的資料箱服務收集的記錄。 


## <a name="about-audit-logs"></a>關於 audit 記錄 

在您的資料箱上，會收集下列記錄：

- **系統記錄**-資料箱是以 Windows 為基礎的裝置，會記錄所有硬體、軟體和系統事件。 系統 audit 記錄中會收集並報告這些事件的集合。 

- 資料箱是以 Windows 為基礎的裝置，則會記錄**所有安全性事件**。 這些事件集會收集並報告在安全性 audit 記錄中。 

- **應用程式**-這些記錄僅適用于資料箱。 這些記錄包含裝置上產生的所有事件，以回應正在執行的資料箱服務。

下一節將討論每個記錄。

### <a name="system-logs"></a>系統記錄檔

下列系統記錄檔事件識別碼會收集為您資料箱上的系統 audit 記錄：

|事件提供者名稱     |已收集事件識別碼   |事件描述   |
|-------------------|----------|----------------|
|Microsoft-Windows-核心-一般|12  |OS 重新開機的 UTC 時間。   |
|                                |13  |OS 關閉時的 UTC 時間。 |
|    |                              |
|Microsoft-Windows-核心-電源  |41  |系統重新開機，但沒有正常關機。| 
|    |                              |
|Microsoft-Windows-BitLocker-驅動程式|全部|    |

### <a name="security-logs"></a>安全性記錄

系統會在您的資料箱上將下列安全性記錄檔事件識別碼收集為安全性 audit 記錄：

|事件提供者名稱                   |已收集事件識別碼    |事件描述       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-Security-Auditing   |4624        |成功登入。 |
|                                      |4625        |帳戶登入失敗。 未知的使用者名稱或密碼錯誤。 |
|                                     

### <a name="application-logs"></a>應用程式記錄

下列應用程式記錄檔事件識別碼會收集為資料箱上封裝 audit 記錄的一部分。     

- **Microsoft-DataBox-OOBE** -包含本機 UI 中發生的事件。 
- **DataBox-重新布建-Audit** -包含與資料箱裝置的重新布建相關的事件。 當透過本機 UI 重設裝置時，會發生資料箱的重新布建。 當您想要清除已複製的資料，方法是移除現有的共用，並在重新布建或裝置重設過程中重新建立共用，即可選擇此選項。
- **DataBox-HcsMgmt-Audit** -包含只與**準備寄送**步驟相關的事件，然後再將裝置送回至 Azure 資料中心。 
- **DataBox-IfxAudit** -包含由產品的不同實體所記錄的訊息，這些作業會指出有關某些流程中發生之情況的詳細資訊。

以下資料表摘要說明各種事件提供者，以及每個案例中所收集的對應事件識別碼。

|事件提供者名稱    |事件識別碼    | 附註 |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-DataBox-OOBE-審核 |4624        |成功登入。|
|                                      |4625        |帳戶登入失敗。 未知的使用者名稱或密碼錯誤。|
|                                     |4634        |登出事件。|
|                                   |  | |
|Microsoft-DataBox-重新布建-Audit    |65001       |成功的重新布建事件。|
|                                                  |65002       |無法重新布建事件。|
|                                                  |                 |         |
|Microsoft-DataBox-HcsMgmt-Audit        |65003       |寄送準備狀態事件 NotStarted，InProgress，失敗，已取消，成功，ScanCompletedWithIssues，SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-DataBox-IfxAudit    |全部 |所有事件都會以 audit log API 記錄在程式碼中 |

以下是檢測架構 (IFX) audit 記錄檔的範例：

|     工作/作業/API                              |     記錄的事件                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     清除                                   |     記錄清除作業的啟動、完成或失敗相關事件。 |                                              
|     準備裝置以供客戶出貨    |     記錄開始、完成或失敗的工作，以準備寄送裝置的相關事件。 |
|     佈建                                 |     會記錄與啟動、完成或失敗的裝置布建作業相關的事件。|
|     Audit package 作業                       |     會記錄與建立監管記錄之鏈的啟動、完成或失敗相關的事件。|
|     磁片覆寫                          |     會記錄無法覆寫磁片的問題。|
|     啟用或停用遠端 PowerShell     |     記錄在裝置上啟用或停用遠端 PowerShell 的相關事件。 |
|     取得安裝階段詳細資料               |     在階段中，與裝置上的軟體安裝相關的事件會記錄在 Azure 資料中心。|
|     解除鎖定或鎖定 BitLocker 磁片區           |     系統會記錄事件，以指出*basevolume*和*hcsdata*磁片區的 BitLocker 狀態。|
|     淨化磁片                              |     與無法清除的實體磁片失敗相關的事件，以及已成功清除裝置上所有實體磁片的事件。 |
|     啟用或停用本機使用者               |     記錄與啟用或停用 StorSimpleAdmin 和 PodSupportAdminUser 本機使用者帳戶相關的事件。| 
|     密碼重設                          |     會記錄與本機 StorSimpleAdmin 使用者的密碼重設成功或失敗相關的事件。 |


除了 IFX audit 記錄之外，也會針對資料箱收集監管的審核記錄。 這些記錄檔無法即時查看，但只能在工作完成後，從資料箱磁片清除資料。 這些記錄包含 IFX audit 記錄中包含的資訊子集。

如需有關監管審核記錄鏈的詳細資訊，請參閱[在資料抹除之後取得保管鏈](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure)。

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>存取稽核記錄

這些記錄會儲存在 Azure 中，而且無法直接存取。 如果您需要存取這些記錄檔，請提出支援票證。 如需詳細資訊，請參閱 [連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)。 

一旦提出支援票證，Microsoft 將會下載並提供這些記錄的存取權。


## <a name="next-steps"></a>後續步驟

- 瞭解如何[針對資料箱和 Data Box Heavy 上的問題進行疑難排解](data-box-troubleshoot.md)。
