---
title: 具有 AD FS 具風險的 IP 報告的 Azure AD Connect Health |Microsoft Docs
description: 描述 Azure AD Connect Health AD FS 具風險的 IP 報告。
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: defdf8118f1b07f8d6ddc4d232cda0fc423ef9f6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897266"
---
# <a name="risky-ip-report-public-preview"></a>有風險的 IP 報告（公開預覽）
AD FS 客戶可能會向網際網路公開密碼驗證端點，以提供驗證服務讓終端使用者能夠存取 Office 365 之類的 SaaS 應用程式。 在此情況下，不良執行者便有機會嘗試登入您的 AD FS 系統，以猜出終端使用者的密碼並存取應用程式資源。 自 Windows Server 2012 R2 中的 AD FS 開始，AD FS 會提供外部網路帳戶鎖定功能以避免這類攻擊。 如果您使用較低的版本，我們強烈建議您將 AD FS 系統升級至 Windows Server 2016。 <br />

此外，單一 IP 位址也有可能會嘗試多次登入多個使用者。 在這些情況下，每位使用者的嘗試次數可能會低於 AD FS 中帳戶鎖定保護的閾值。 Azure AD Connect Health 現在提供「具風險的 IP 報告」，此報告會偵測這種狀況，並在發生此狀況時通知系統管理員。 此報告的主要優點如下： 
- 可偵測到超過失敗密碼型登入閾值的 IP 位址
- 支援由於不正確密碼或外部網路鎖定狀態所導致的失敗登入
- 電子郵件通知，可在發生此狀況時立即向系統管理員發出警示，並可自訂電子郵件設定
- 可自訂的閾值設定，以符合組織的安全性原則
- 可下載的報告，以供進行離線分析以及透過自動化與其他系統整合

> [!NOTE]
> 若要使用此報告，您必須確定已啟用 AD FS 稽核。 如需詳細資訊，請參閱 [啟用 AD FS 的稽核](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs)。 <br />
> 若要存取預覽，則需要全域管理員或[安全性讀取者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)的權限。  
> 

## <a name="what-is-in-the-report"></a>報表中的內容為何？
失敗的登入活動用戶端 IP 位址是透過 Web 應用程式 Proxy 伺服器進行匯總。 「具風險的 IP 報告」中的每個項目會顯示有關已超過指定閾值之失敗 AD FS 登入活動的彙總資訊。 此報告可提供下列資訊：![Azure AD Connect Health 入口網站](./media/how-to-connect-health-adfs/report4a.png)

| 報告項目 | 說明 |
| ------- | ----------- |
| 時間戳記 | 根據 Azure 入口網站的本機時間，顯示偵測時間範圍開始時的時間戳記。<br /> 所有每日事件都會在午夜 UTC 時間產生。 <br />每小時事件的時間戳記會四捨五入到該小時的開始。 您可以在所匯出檔案的 “firstAuditTimestamp” 中找到第一個活動的開始時間。 |
| 觸發程序類型 | 顯示偵測時間範圍的類型。 彙總觸發程序類型是每小時或每日。 這有助於偵測嘗試次數會分散在整天的高頻率暴力密碼破解攻擊與緩慢攻擊。 |
| IP 位址 | 具有不正確密碼或外部網路鎖定登入活動的單一「具風險的 IP 位址」。 這可以是 IPv4 或 IPv6 位址。 |
| 不正確密碼錯誤計數 | 在偵測時間範圍內，從 IP 位址所發生的不正確密碼錯誤計數。 某些使用者可能會多次發生不正確密碼錯誤。 請注意，這不包括由於密碼過期所導致的失敗嘗試。 |
| 外部網路鎖定錯誤計數 | 在偵測時間範圍內，從 IP 位址所發生的外部網路鎖定錯誤計數。 某些使用者可能會多次發生外部網路鎖定錯誤。 必須在 AD FS (2012R2 版或更高版本) 中設定外部網路鎖定，才能看到此計數。 <b>注意</b>：我們強烈建議您在允許使用密碼進行外部網路登入時開啟這項功能。 |
| 嘗試的唯一使用者 | 在偵測時間範圍內，從 IP 位址所嘗試的唯一使用者帳戶計數。 這個機制可供用來區別單一使用者攻擊模式與多使用者攻擊模式。  |

例如，下列報表項目指出，從 02/28/2018 下午 6 點到下午 7 點這一小時的範圍內，IP 位址 104.2XX.2XX.9<i></i> 沒有任何不正確密碼錯誤，但有 284 個外部網路鎖定錯誤。 在此條件下，有 14 個唯一使用者受到影響。 活動事件已超過指定的報告每小時閾值。 

![Azure AD Connect Health 入口網站](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - 此報告清單中只會顯示超過指定閾值的活動。 
> - 此報表最多只能往回追蹤 30 天。
> - 此警示報告不會顯示 Exchange IP 位址或私人 IP 位址。 它們仍是包含在匯出清單中。 
>

![Azure AD Connect Health 入口網站](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>清單中的負載平衡器 IP 位址
負載平衡器會彙總失敗的登入活動，並觸發警示閾值。 如果您看見負載平衡器 IP 位址，則很可能表示您的外部負載平衡器未在將要求傳遞至 Web 應用程式 Proxy 伺服器時傳送用戶端 IP 位址。 請正確設定您的負載平衡器，以傳遞轉送用戶端 IP 位址。 

## <a name="download-risky-ip-report"></a>下載具風險的 IP 報告 
使用**下載**功能，即可從 Connect Health 入口網站匯出過去 30 天內的整個「具風險的 IP 位址」清單。匯出結果會包含每個偵測時間範圍中的所有失敗 AD FS 登入活動，以供您在匯出後自訂篩選。 除了入口網站中醒目提示的彙總外，匯出結果還會顯示有關每一 IP 位址之失敗登入活動的更多詳細資料：

|  報告項目  |  說明  | 
| ------- | ----------- | 
| firstAuditTimestamp | 顯示屬於偵測時間範圍內失敗活動開始時間的第一個時間戳記。  | 
| lastAuditTimestamp | 顯示屬於偵測時間範圍內失敗活動結束時間的最後一個時間戳記。  | 
| attemptCountThresholdIsExceeded | 目前的活動超過警示閾值時的旗標。  | 
| isWhitelistedIpAddress | 從警示和報告中篩選出 IP 位址時的旗標。 會篩選出私人 IP 位址 (10.x.x.x、172.x.x.x 和 192.168.x.x<i></i>) 和 Exchange IP 位址，並標示為 True。 如果您看見私人 IP 位址範圍，則很可能表示您的外部負載平衡器未在將要求傳遞至 Web 應用程式 Proxy 伺服器時傳送用戶端 IP 位址。  | 

## <a name="configure-notification-settings"></a>設定通知設定
您可以透過**通知設定**更新報告的管理員連絡人。 根據預設，具風險之 IP 的警示電子郵件通知是關閉狀態。 您可以切換 [在有 IP 位址超過失敗活動閾值的報告時取得電子郵件通知] 下的按鈕來啟用通知。含 Connect Health 中的一般警示通知設定一樣，其可讓您在這裡自訂有關「具風險的 IP 報告」的指定通知收件者清單。 您也可以在進行變更時通知所有全域管理員。 

## <a name="configure-threshold-settings"></a>設定閾值設定
您可以透過 [閾值設定] 更新警示閾值。 一開始，系統依預設會設定閾值。 「具風險的 IP 報告」閾值設定中有四個類別：

![Azure AD Connect Health 入口網站](./media/how-to-connect-health-adfs/report4d.png)

| 閾值項目 | 說明 |
| --- | --- |
| (錯誤 U/P + 外部網路鎖定) / 天  | 當每**天**的不正確密碼計數加上外部網路鎖定計數超過此閾值設定時，系統會報告活動和觸發程序警示通知。 |
| (錯誤 U/P + 外部網路鎖定) / 小時 | 當每**小時**的不正確密碼計數加上外部網路鎖定計數超過此閾值設定時，系統會報告活動和觸發程序警示通知。 |
| 外部網路鎖定 / 天 | 當每**天**的外部網路鎖定計數超過此閾值設定時，系統會報告活動和觸發程序警示通知。 |
| 外部網路鎖定 / 小時| 當每**小時**的外部網路鎖定計數超過此閾值設定時，系統會報告活動和觸發程序警示通知。 |

> [!NOTE]
> - 報告閾值的變更會在設定變更一小時後套用。 
> - 現有的報表項目不會受到閾值變更所影響。 
> - 建議您分析環境內所看到的事件數目，並適當地調整閾值。 
>
>

## <a name="faq"></a>常見問題集
**為什麼我會在報告中看到私人 IP 位址範圍？**  <br />
系統會篩選出私人 IP 位址 (10.x.x.x、172.x.x.x 和 192.168.x.x<i></i>) 和 Exchange IP 位址，並在 IP 允許清單中將這些位址標示為 True。 如果您看見私人 IP 位址範圍，則很可能表示您的外部負載平衡器未在將要求傳遞至 Web 應用程式 Proxy 伺服器時傳送用戶端 IP 位址。

**為什麼我會在報告中看到負載平衡器的 IP 位址？**  <br />
如果您看見負載平衡器 IP 位址，則很可能表示您的外部負載平衡器未在將要求傳遞至 Web 應用程式 Proxy 伺服器時傳送用戶端 IP 位址。 請正確設定您的負載平衡器，以傳遞轉送用戶端 IP 位址。 

**我該怎麼做才能封鎖 IP 位址？**  <br />
請將已識別出的惡意 IP 位址新增至防火牆，或在 Exchange 中封鎖。   <br />

**為什麼我在此報表中看不到任何專案？** <br />
- 失敗登入活動未超過閾值設定。
- 請確定 AD FS 伺服器清單中沒有作用中的「健康情況服務不是最新狀態」警示。  深入了解[如何針對此警示進行疑難排解](how-to-connect-health-data-freshness.md)。
- AD FS 伺服器陣列中未啟用稽核。

**為什麼我看不到報表的存取權？**  <br />
需要全域管理員或[安全性讀取者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)權限。 請連絡您的全域管理員以取得存取權。


## <a name="next-steps"></a>後續步驟
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health 代理程式安裝](how-to-connect-health-agent-install.md)
