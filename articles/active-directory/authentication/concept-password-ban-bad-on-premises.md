---
title: Azure AD 密碼保護-Azure Active Directory
description: 使用 Azure AD 密碼保護，禁止內部部署 Active Directory Domain Services 環境中的弱式密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: feb7c4a4417d64e039793bd96141c965f6437414
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050922"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>強制執行內部部署 Azure AD Active Directory Domain Services 的密碼保護

Azure AD 密碼保護會偵測並封鎖已知的弱式密碼及其變體，同時也會封鎖貴組織特定的其他弱式詞彙。 Azure AD 密碼保護的內部部署，會使用儲存在 Azure AD 中的相同全域和自訂禁用密碼清單，而且會對內部部署密碼變更進行相同的檢查，如同 Azure AD 用於雲端式變更的情況。 這些檢查會在對內部部署 Active Directory Domain Services （AD DS）網域控制站進行密碼變更和密碼重設事件期間執行。

## <a name="design-principles"></a>設計原則

Azure AD 密碼保護的設計是以下列原則為考慮：

* 網域控制站（Dc）永遠不需要直接與網際網路通訊。
* Dc 上不會開啟任何新的網路埠。
* 不需要任何 AD DS 的架構變更。 軟體會使用現有的 AD DS*容器*和*serviceConnectionPoint*架構物件。
* 不需要最低 AD DS 網域或樹系功能等級（DFL/FFL）。
* 軟體不會在其保護的 AD DS 網域中建立或要求帳戶。
* 使用者純文字密碼永遠不會離開網域控制站，不論是在密碼驗證作業或任何其他時間。
* 軟體不依賴其他 Azure AD 功能。 例如，Azure AD 密碼雜湊同步處理（PHS）不相關，或 Azure AD 密碼保護所需。
* 支援累加式部署，不過，只有在安裝網域控制站代理程式（DC 代理程式）的情況下，才會強制執行密碼原則。

## <a name="incremental-deployment"></a>累加式部署

Azure AD 密碼保護支援在 AD DS 網域中跨 Dc 進行增量部署。 請務必瞭解這是什麼意思，以及取捨的意義。

Azure AD 密碼保護 DC 代理程式軟體只能驗證安裝在 DC 上的密碼，而且僅適用于傳送至該 DC 的密碼變更。 不可能控制由 Windows 用戶端電腦選擇哪些 Dc 來處理使用者密碼變更。 為了保證一致的行為和通用 Azure AD 密碼保護安全性強制執行，必須在網域中的所有 Dc 上安裝 DC 代理程式軟體。

許多組織都想要在進行完整部署之前，先仔細測試其 Dc 子集的 Azure AD 密碼保護。 為了支援此案例，Azure AD 密碼保護支援部分部署。 即使網域中的其他 Dc 並未安裝 DC 代理程式軟體，指定 DC 上的 DC 代理程式軟體也會主動驗證密碼。 這種類型的部分部署並不安全，而且不建議用於測試用途。

## <a name="architectural-diagram"></a>架構圖

在內部部署 AD DS 環境中部署 Azure AD 密碼保護之前，請務必先瞭解基礎設計和功能概念。 下圖顯示 Azure AD 密碼保護的元件如何一起運作：

![Azure AD 密碼保護元件如何一起運作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 的密碼保護 Proxy 服務會在目前 AD DS 樹系中任何已加入網域的電腦上執行。 服務的主要目的是要將密碼原則下載要求從 Dc 轉寄至 Azure AD，然後將 Azure AD 的回應傳回 DC。
* DC 代理程式的密碼篩選 DLL 會接收來自作業系統的使用者密碼驗證要求。 篩選器會將它們轉送到在 DC 本機上執行的 DC 代理程式服務。
* Azure AD 密碼保護的 DC 代理程式服務會接收來自 DC 代理程式密碼篩選 DLL 的密碼驗證要求。 DC 代理程式服務會使用目前（本機可用）的密碼原則來處理它們，並*傳回**成功或失敗*的結果。

## <a name="how-azure-ad-password-protection-works"></a>Azure AD 密碼保護的運作方式

內部部署 Azure AD 密碼保護元件的操作方式如下：

1. 每個 Azure AD 密碼保護 Proxy 服務實例會在 Active Directory 中建立*serviceConnectionPoint*物件，以向樹系中的 dc 通告自己。

    每個用於 Azure AD 密碼保護的 DC 代理程式服務也會在 Active Directory 中建立*serviceConnectionPoint*物件。 此物件主要用於報告和診斷。

1. DC 代理程式服務負責起始從 Azure AD 下載新的密碼原則。 第一個步驟是藉由查詢樹系中的 proxy *serviceConnectionPoint*物件，來找出 Azure AD 的密碼保護 Proxy 服務。

1. 當找到可用的 proxy 服務時，DC 代理程式會將密碼原則下載要求傳送至 proxy 服務。 Proxy 服務接著會將要求傳送至 Azure AD，然後將回應傳回 DC 代理程式服務。

1. 在 DC 代理程式服務從 Azure AD 收到新的密碼原則之後，服務會將該原則儲存在其網域*sysvol*資料夾共用根目錄的專用資料夾中。 DC 代理程式服務也會監視此資料夾，以防較新的原則從網域中的其他 DC 代理程式服務複寫。

1. DC 代理程式服務一律會在服務啟動時要求新的原則。 在 DC 代理程式服務啟動之後，它會每小時檢查目前本機可用原則的存留期。 如果原則超過一小時，DC 代理程式會透過 proxy 服務向 Azure AD 要求新的原則，如先前所述。 如果目前的原則未超過一小時，DC 代理程式會繼續使用該原則。

1. DC 收到密碼變更事件時，會使用快取的原則來判斷新密碼是否被接受或拒絕。

### <a name="key-considerations-and-features"></a>主要考慮和功能

* 每當下載 Azure AD 密碼保護密碼原則時，該原則就是租使用者特有的原則。 換句話說，密碼原則一律是 Microsoft 全域禁用密碼清單和每一租使用者自訂禁用密碼清單的組合。
* DC 代理程式會透過 TCP 透過 RPC 與 proxy 服務進行通訊。 Proxy 服務會在動態或靜態 RPC 埠上接聽這些呼叫，視設定而定。
* DC 代理程式永遠不會在網路可用的埠上接聽。
* Proxy 服務永遠不會呼叫 DC 代理程式服務。
* Proxy 服務是無狀態的。 它永遠不會快取從 Azure 下載的原則或任何其他狀態。
* DC 代理程式服務一律會使用最新的本機可用密碼原則來評估使用者的密碼。 如果本機 DC 上沒有可用的密碼原則，則會自動接受密碼。 發生這種情況時，會記錄事件訊息以警告系統管理員。
* Azure AD 密碼保護不是即時原則應用程式引擎。 Azure AD 中的密碼原則設定變更時，以及變更何時達到並在所有 Dc 上強制執行時，可能會有延遲。
* Azure AD 密碼保護作為現有 AD DS 密碼原則的補充，而不是取代。 這包括任何可能安裝的其他協力廠商密碼篩選 dll。 AD DS 一律要求所有密碼驗證元件必須先同意，才能接受密碼。

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Azure AD 密碼保護的樹系/租使用者系結

AD DS 樹系中的 Azure AD 密碼保護部署需要向 Azure AD 註冊該樹系。 部署的每個 proxy 服務也必須向 Azure AD 註冊。 這些樹系和 proxy 註冊會與特定的 Azure AD 租使用者相關聯，這是由註冊期間所使用的認證隱含識別。

樹系中的 AD DS 樹系和所有已部署的 proxy 服務都必須向相同的租使用者註冊。 不支援將該樹系中的 AD DS 樹系或任何 proxy 服務註冊到不同的 Azure AD 租使用者。 這類錯誤設定部署的徵兆包括無法下載密碼原則。

## <a name="download"></a>下載

您可以從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)取得 Azure AD 密碼保護的兩個必要代理程式安裝程式。

## <a name="next-steps"></a>後續步驟

若要開始使用內部部署 Azure AD 密碼保護，請完成下列操作說明：

> [!div class="nextstepaction"]
> [部署內部部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
