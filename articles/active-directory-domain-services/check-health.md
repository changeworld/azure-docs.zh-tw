---
title: 檢查 Azure 活動目錄域服務的運行狀況 |微軟文件
description: 瞭解如何檢查 Azure 活動目錄域服務 (Azure AD DS) 託管域的運行狀況,以及使用 Azure 門戶瞭解狀態消息。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 27ab14af25704a4f7fb46aa5e86cdaf881c49442
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655645"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>檢查 Azure 活動目錄域服務託管域的運行狀況

Azure 活動目錄域服務 (Azure AD DS) 運行一些後台任務,以保持託管域的正常運行和最新。 這些任務包括執行備份、應用安全更新和同步 Azure AD 中的數據。 如果 Azure AD DS 託管域出現問題,則這些任務可能無法成功完成。 要查看和解決任何問題,可以使用 Azure 門戶檢查 Azure AD DS 託管域的運行狀況狀態。

本文介紹如何查看 Azure AD DS 運行狀況狀態並瞭解顯示的資訊或警報。

## <a name="view-the-health-status"></a>檢視執行狀況

使用 Azure 門戶查看 Azure AD DS 託管域的運行狀況狀態。 可以看到有關上次備份時間和與 Azure AD 同步的資訊,以及指示託管域運行狀況有問題的任何警報。 要檢視 Azure AD DS 託管域的執行狀況狀態,完成以下步驟:

1. 在 Azure 門戶中,搜尋並選擇**Azure AD 網域服務**。
1. 選擇 Azure AD DS 託管域,如*aaddscontoso.com*。
1. 在 Azure AD DS 資源視窗的左側,選擇 **「運行狀況**」 。 以下範例螢幕截圖顯示了一個健康的 Azure AD DS 託管域以及上次備份和 Azure AD 同步的狀態:

    ![Azure 門戶中的執行狀況頁概述,顯示 Azure 活動目錄域服務狀態](./media/check-health/health-page.png)

運行狀況頁*的最後一個計算*時間戳顯示上次檢查 Azure AD DS 託管域的時間。 Azure AD DS 託管域的運行狀況每小時評估一次。 如果對 Azure AD DS 託管域進行任何更改,請等到下一個評估週期查看更新的運行狀況。

右上角的狀態指示 Azure AD DS 託管域的總體運行狀況。 狀態因素會影響域上的所有現有警報。 下表詳細介紹了可用狀態指示器:

| 狀態 | 圖示 | 說明 |
| --- | :----: | --- |
| 執行中 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Azure AD DS 託管域運行正常,沒有任何嚴重或警告警報。 域可能具有資訊警報。 |
| 需要注意(警告) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Azure AD DS 託管域上沒有嚴重警報,但應解決一個或多個警告警報。 |
| 需要注意(關鍵) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Azure AD DS 託管域上存在一個或多個關鍵警報,必須加以解決。 您可能還有警告和/或資訊警報。 |
| 正在部署 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | 正在部署 Azure AD DS 域。 |

## <a name="understand-monitors-and-alerts"></a>瞭解監視器和警報

Azure AD DS 託管域的執行狀況顯示兩種類型的資訊 -*監視器*與*警示*。 監視器顯示核心後台任務完成的時間。 警報提供資訊或建議以提高託管域的穩定性。

### <a name="monitors"></a>監視器

監視器是定期檢查的 Azure AD DS 託管域的區域。 如果 Azure AD DS 託管域有任何活動警報,則可能導致其中一個監視器報告問題。 Azure AD 網域服務目前具有以下區域的監視器:

* Backup
* 與 Azure AD 的同步處理

#### <a name="backup-monitor"></a>備份監視器

備份監視器檢查 Azure AD DS 託管域的自動定期備份成功運行。 下表詳細介紹了可用的備份監視器狀態:

| 詳細資料值 | 說明 |
| --- | --- |
| 從不備份 | 對於新的 Azure AD DS 託管域,此狀態是正常的。 第一個備份應在部署 Azure AD DS 託管域 24 小時後創建。 如果此狀態仍然存在,[請開啟 Azure 支援要求][azure-support]。 |
| 上次備份時間是在 1 到 14 天之前 | 此時間範圍是備份監視器的預期狀態。 在此期間應進行自動定期備份。 |
| 上次備份時間已超過 14 天之前。 | 時間跨度超過兩周表示自動定期備份出現問題。 活動嚴重警報可能會阻止備份 Azure AD DS 託管域。 解決 Azure AD DS 託管域的任何活動警報。 如果備份監視器未更新狀態以回報最近的備份,[請開啟 Azure 支援要求][azure-support]。 |

#### <a name="synchronization-with-azure-ad-monitor"></a>與 Azure AD 監視器同步

Azure AD DS 託管域定期與 Azure 活動目錄同步。 使用者和組物件數以及自上次同步以來 Azure AD 目錄中所做的更改數會影響同步所需的時間。 如果 Azure AD DS 託管域上次同步超過三天前,請檢查並解決任何活動警報。 如果同步監視器未更新狀態以在您處理任何作用時顯示最近的同步,[請開啟 Azure 支援請求][azure-support]。

### <a name="alerts"></a>警示

針對 Azure AD DS 託管域中需要解決的問題生成警報,這些問題需要解決,以便服務正確運行。 每個警報都解釋了問題,並給出了一個 URL,其中概述了解決問題的具體步驟。 有關可能的警報及其解決方法的詳細資訊,請參閱[故障排除警報](troubleshoot-alerts.md)。

執行狀況警示分為以下嚴重級別:

 * **嚴重警報**是嚴重影響 Azure AD DS 託管域的問題。 應立即處理這些警報。 在問題解決之前,Azure 平台無法監視、管理、修補和同步託管域。
 * 如果問題仍然存在,**警告警報**會通知您可能影響 Azure AD DS 託管域操作的問題。 這些警報還提供保護託管域的建議。
 * **資訊警報**是不會對 Azure AD DS 託管域產生負面影響的通知。 資訊警報提供了對託管域中發生的情況的一些見解。

## <a name="next-steps"></a>後續步驟

有關運行狀況頁中顯示的警報的詳細資訊,請參閱[解決託管域上的警報][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
