---
title: Azure AD 密碼保護-Azure Active Directory
description: 使用 Azure AD 密碼保護，禁止內部部署 Active Directory 中的弱式密碼
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74848641"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>強制執行 Windows Server Active Directory 的 Azure AD 密碼保護

Azure AD 密碼保護是增強組織中密碼原則的功能。 內部部署的密碼保護會同時使用儲存在 Azure AD 中的全域和自訂禁用密碼清單。 它會在內部部署執行相同的檢查，如同 Azure AD 雲端式變更。 這些檢查會在密碼變更和密碼重設案例中執行。

## <a name="design-principles"></a>設計原則

Azure AD 密碼保護的設計是考慮下列原則：

* 網域控制站永遠不需要直接與網際網路通訊。
* 在網域控制站上不會開啟任何新的網路連接埠。
* 不需要 Active Directory 結構描述變更。 軟體會使用現有的 Active Directory**容器**和**serviceConnectionPoint**架構物件。
* 不需要最低 Active Directory 網域或樹系功能等級（DFL/FFL）。
* 軟體不會在其保護的 Active Directory 網域中建立或要求帳戶。
* 使用者純文字密碼永遠不會離開網域控制站，不論是在密碼驗證作業或任何其他時間。
* 軟體與其他 Azure AD 功能無關;例如 Azure AD 密碼雜湊同步處理並不相關，也不需要為了讓 Azure AD 密碼保護運作。
* 支援累加式部署，不過，只有在安裝網域控制站代理程式（DC 代理程式）的情況下，才會強制執行密碼原則。 如需詳細資訊，請參閱下一個主題。

## <a name="incremental-deployment"></a>累加式部署

Azure AD 密碼保護支援 Active Directory 網域中的網域控制站進行累加式部署，但請務必瞭解這是什麼意思，以及取捨的意義。

Azure AD 密碼保護 DC 代理程式軟體只能驗證安裝在網域控制站上的密碼，以及僅適用于傳送到該網域控制站的密碼變更。 您無法控制由 Windows 用戶端電腦選擇哪些網域控制站來處理使用者密碼變更。 為了保證一致的行為和通用密碼保護安全性強制執行，必須將 DC 代理程式軟體安裝在網域中的所有網域控制站上。

在進行完整部署之前，許多組織會想要仔細測試其網域控制站子集的 Azure AD 密碼保護。 Azure AD 密碼保護支援部分部署，即使網域中的其他 Dc 未安裝 DC 代理程式軟體，指定 DC 上的 DC 代理程式軟體仍會主動驗證密碼。 這種類型的部分部署並不安全，而且不建議用於測試用途。

## <a name="architectural-diagram"></a>架構圖

在內部部署 Active Directory 環境中部署 Azure AD 密碼保護之前，請務必先瞭解基礎設計和功能概念。 下圖顯示密碼保護的元件如何搭配使用：

![Azure AD 密碼保護元件搭配運作的方式](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 密碼保護 Proxy 服務會在目前的 Active Directory 樹系中任何已加入網域的機器上執行。 其主要目的是將來自網域控制站的密碼原則下載要求轉寄到 Azure AD。 然後，它會將來自 Azure AD 的回應傳回網域控制站。
* DC 代理程式的密碼篩選 DLL 會接收來自作業系統的使用者密碼驗證要求。 它會將它們轉送到網域控制站上本機執行的 DC 代理程式服務。
* 密碼保護的 DC 代理程式服務會接收來自 DC 代理程式密碼篩選 DLL 的密碼驗證要求。 它會使用目前（本機可用）的密碼原則來處理它們，並傳回結果：*通過*或*失敗*。

## <a name="how-password-protection-works"></a>密碼保護的運作方式

每個 Azure AD 密碼保護 Proxy 服務實例會在 Active Directory 中建立**serviceConnectionPoint**物件，以向樹系中的網域控制站通告自己。

每個用於密碼保護的 DC 代理程式服務也會在 Active Directory 中建立**serviceConnectionPoint**物件。 此物件主要用於報告和診斷。

DC 代理程式服務負責起始從 Azure AD 下載新的密碼原則。 第一個步驟是藉由查詢樹系中的 proxy **serviceConnectionPoint**物件，來找出 Azure AD 的密碼保護 Proxy 服務。 當找到可用的 proxy 服務時，DC 代理程式會將密碼原則下載要求傳送至 proxy 服務。 Proxy 服務接著會將要求傳送至 Azure AD。 Proxy 服務接著會傳回 DC 代理程式服務的回應。

在 DC 代理程式服務從 Azure AD 收到新的密碼原則之後，服務會將該原則儲存在其網域*sysvol*資料夾共用根目錄的專用資料夾中。 DC 代理程式服務也會監視此資料夾，以防較新的原則從網域中的其他 DC 代理程式服務複寫。

DC 代理程式服務一律會在服務啟動時要求新的原則。 在 DC 代理程式服務啟動之後，它會每小時檢查目前本機可用原則的存留期。 如果原則超過一小時，DC 代理程式會透過 proxy 服務向 Azure AD 要求新的原則，如先前所述。 如果目前的原則未超過一小時，DC 代理程式會繼續使用該原則。

每當下載 Azure AD 密碼保護密碼原則時，該原則就是租使用者特有的原則。 換句話說，密碼原則一律是 Microsoft 全域禁用密碼清單和每一租使用者自訂禁用密碼清單的組合。

DC 代理程式會透過 TCP 透過 RPC 與 proxy 服務進行通訊。 Proxy 服務會在動態或靜態 RPC 埠上接聽這些呼叫，視設定而定。

DC 代理程式永遠不會在網路可用的埠上接聽。

Proxy 服務永遠不會呼叫 DC 代理程式服務。

Proxy 服務是無狀態的。 它永遠不會快取從 Azure 下載的原則或任何其他狀態。

DC 代理程式服務一律會使用最新的本機可用密碼原則來評估使用者的密碼。 如果本機 DC 上沒有可用的密碼原則，則會自動接受密碼。 發生這種情況時，會記錄事件訊息以警告系統管理員。

Azure AD 密碼保護不是即時原則應用程式引擎。 Azure AD 中的密碼原則設定變更時，以及該變更何時達到並在所有網域控制站上強制執行時，可能會有延遲。

Azure AD 密碼保護作為現有 Active Directory 密碼原則的補充，而不是取代。 這包括任何可能安裝的其他協力廠商密碼篩選 dll。 Active Directory 一律要求所有密碼驗證元件必須先同意，才能接受密碼。

## <a name="foresttenant-binding-for-password-protection"></a>密碼保護的樹系/租使用者系結

Active Directory 樹系中的 Azure AD 密碼保護部署需要向 Azure AD 註冊該樹系。 每個部署的 proxy 服務也必須向 Azure AD 註冊。 這些樹系和 proxy 註冊會與特定的 Azure AD 租使用者相關聯，這是由註冊期間所使用的認證隱含識別。

樹系中的 Active Directory 樹系和所有已部署的 proxy 服務都必須向相同的租使用者註冊。 不支援將該樹系中的 Active Directory 樹系或任何 proxy 服務註冊到不同的 Azure AD 租使用者。 這類錯誤設定部署的徵兆包括無法下載密碼原則。

## <a name="download"></a>下載

您可以從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)取得 Azure AD 密碼保護的兩個必要代理程式安裝程式。

## <a name="next-steps"></a>後續步驟
[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
