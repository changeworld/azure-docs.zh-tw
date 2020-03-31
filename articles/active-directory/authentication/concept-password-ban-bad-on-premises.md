---
title: Azure AD 密碼保護 - Azure 活動目錄
description: 通過使用 Azure AD 密碼保護，禁止本地活動目錄中的弱密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848641"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>強制執行 Windows Server Active Directory 的 Azure AD 密碼保護

Azure AD 密碼保護是增強組織中密碼原則的功能。 密碼保護的本地部署使用存儲在 Azure AD 中的全域和自訂禁止密碼清單。 它在本地執行與 Azure AD 對基於雲的更改相同的檢查。 這些檢查在密碼更改和密碼重設方案期間執行。

## <a name="design-principles"></a>設計原則

Azure AD 密碼保護的設計考慮到了以下原則：

* 網域控制站永遠不必與互聯網直接通信。
* 在網域控制站上不會開啟任何新的網路連接埠。
* 不需要 Active Directory 結構描述變更。 該軟體使用現有的活動目錄**容器****和服務連接點**架構物件。
* 不需要最小活動目錄域或林功能級別 （DFL/FFL）。
* 該軟體不創建或要求帳戶在活動目錄域中，它保護。
* 使用者純文字密碼永遠不會離開網域控制站，無論是在密碼驗證操作期間還是在任何其他時間。
* 該軟體不依賴于其他 Azure AD 功能;因此，該軟體不依賴于其他 Azure AD 功能。例如，Azure AD 密碼雜湊同步不相關，並且不需要 Azure AD 密碼保護才能正常工作。
* 支援增量部署，但密碼原則僅在安裝網域控制站代理 （DC 代理）時強制執行。 有關詳細資訊，請參閱下一個主題。

## <a name="incremental-deployment"></a>增量部署

Azure AD 密碼保護支援在 Active Directory 域中的網域控制站之間增量部署，但瞭解這真正含義和權衡非常重要。

Azure AD 密碼保護 DC 代理軟體只能在安裝在網域控制站上時驗證密碼，並且只能驗證發送到該網域控制站的密碼更改。 無法控制 Windows 用戶端電腦選擇哪些網域控制站來處理使用者密碼更改。 為了保證一致的行為和通用的密碼保護安全實施，DC 代理軟體必須安裝在域中的所有網域控制站上。

許多組織希望在執行完全部署之前對其網域控制站的子集仔細測試 Azure AD 密碼保護。 Azure AD 密碼保護確實支援部分部署，即給定 DC 上的 DC 代理軟體將主動驗證密碼，即使域中的其他 DC 未安裝 DC 代理軟體也是如此。 此類型的部分部署不安全，不建議出於測試目的以外的部署。

## <a name="architectural-diagram"></a>架構圖

在本地活動目錄環境中部署 Azure AD 密碼保護之前，瞭解基礎設計和功能概念非常重要。 下圖顯示了密碼保護元件如何協同工作：

![Azure AD 密碼保護元件搭配運作的方式](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 密碼保護 Proxy 服務會在目前的 Active Directory 樹系中任何已加入網域的機器上執行。 其主要目的是將密碼原則下載請求從網域控制站轉發到 Azure AD。 然後，它將 Azure AD 的回應返回到網域控制站。
* DC 代理的密碼篩選器 DLL 接收來自作業系統的使用者密碼驗證請求。 它將它們轉發到在網域控制站上本地運行的 DC 代理服務。
* 密碼保護的 DC 代理服務接收來自 DC 代理的密碼篩選器 DLL 的密碼驗證請求。 它使用當前（本地可用）密碼原則處理它們，並返回結果：*通過*或*失敗*。

## <a name="how-password-protection-works"></a>密碼保護的工作原理

每個 Azure AD 密碼保護代理服務實例通過在 Active Directory 中創建服務**ConnectPoint**物件，向林中的網域控制站通告自身。

每個用於密碼保護的 DC 代理服務還會在活動目錄中創建**一個服務連接點**物件。 此物件主要用於報告和診斷。

DC 代理服務負責從 Azure AD 啟動新密碼原則的下載。 第一步是通過查詢代理**服務 ConnectPoint**物件的林來查找 Azure AD 密碼保護代理服務。 找到可用的代理服務後，DC 代理會向代理服務發送密碼原則下載請求。 代理服務又將要求傳送到 Azure AD。 然後，代理服務返回對 DC 代理服務的回應。

DC 代理服務從 Azure AD 收到新的密碼原則後，該服務將該策略存儲在其域*sysvol*資料夾共用根目錄的專用資料夾中。 DC 代理服務還會監視此資料夾，以防從域中的其他 DC 代理服務複製更新的策略。

DC 代理服務始終在服務啟動時請求新策略。 啟動 DC 代理服務後，它會每小時檢查當前本地可用策略的年數。 如果策略超過一小時，DC 代理將通過代理服務從 Azure AD 請求新策略，如前所述。 如果當前策略的年長時間不超過一小時，DC 代理將繼續使用該策略。

每當下載 Azure AD 密碼保護密碼原則時，該策略都是特定于租戶的。 換句話說，密碼原則始終是 Microsoft 全域禁止密碼清單和每個租戶自訂禁止密碼清單的組合。

DC 代理通過 TCP 通過 RPC 與代理服務通信。 代理服務偵聽動態或靜態 RPC 埠上的這些調用，具體取決於配置。

DC 代理從不偵聽網路可用埠。

代理服務從不調用 DC 代理服務。

代理服務是無狀態的。 它永遠不會緩存從 Azure 下載的策略或任何其他狀態。

DC 代理服務始終使用最新的本地可用密碼原則來評估使用者的密碼。 如果本地 DC 上沒有可用的密碼原則，則將自動接受密碼。 發生這種情況時，將記錄事件消息以警告管理員。

Azure AD 密碼保護不是即時策略應用程式引擎。 在 Azure AD 中進行密碼原則配置更改和更改達到並在所有網域控制站上強制執行之間可能會有延遲。

Azure AD 密碼保護充當現有活動目錄密碼原則的補充，而不是替換。 這包括可能安裝的任何其他協力廠商密碼篩選器 dll。 活動目錄始終要求所有密碼驗證元件在接受密碼之前都同意。

## <a name="foresttenant-binding-for-password-protection"></a>用於密碼保護的林/租戶綁定

在活動目錄林中部署 Azure AD 密碼保護需要將該林與 Azure AD 註冊。 部署的每個代理服務也必須在 Azure AD 中註冊。 這些林和代理註冊與特定的 Azure AD 租戶相關聯，該租戶由註冊期間使用的憑據隱式標識。

活動目錄林和林中的所有已部署代理服務必須註冊到同一租戶。 不支援將 Active Directory 林或該林中的任何代理服務註冊到不同的 Azure AD 租戶。 此類配置錯誤的部署的症狀包括無法下載密碼原則。

## <a name="download"></a>下載

Azure AD 密碼保護所需的兩個代理安裝程式可從 Microsoft[下載中心](https://www.microsoft.com/download/details.aspx?id=57071)獲得。

## <a name="next-steps"></a>後續步驟
[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
