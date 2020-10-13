---
title: Azure AD 密碼保護-Azure Active Directory
description: 使用 Azure AD 密碼保護，禁止內部部署 Active Directory Domain Services 環境中的弱式密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4802c4faf245819f57f9885129fa876110407dd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965228"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>針對 Active Directory Domain Services 強制執行內部部署 Azure AD 密碼保護

Azure AD 密碼保護會偵測並封鎖已知的弱式密碼及其變體，也可以封鎖您組織專屬的其他弱式詞彙。 Azure AD 密碼保護的內部部署會使用儲存在 Azure AD 中的相同全域和自訂禁用密碼清單，並且會對內部部署密碼的檢查進行相同的檢查，因為 Azure AD 會進行雲端式變更。 針對內部部署 Active Directory Domain Services (AD DS) 網域控制站的密碼變更和密碼重設事件期間，會執行這些檢查。

## <a name="design-principles"></a>設計原則

Azure AD 密碼保護的設計考慮下列原則：

* 網域控制站 (Dc) 永遠不需要直接與網際網路通訊。
* Dc 上未開啟任何新的網路埠。
* 不需要 AD DS 架構變更。 軟體會使用現有的 AD DS *容器* 和 *serviceConnectionPoint* 架構物件。
* 不需要 AD DS 網域或樹系功能等級 (DFL/FFL) 。
* 軟體不會在其保護的 AD DS 網域中建立或要求帳戶。
* 使用者純文字密碼永遠不會離開網域控制站，不論是在密碼驗證作業期間或其他時間。
* 軟體不相依于其他 Azure AD 功能。 例如，Azure AD 密碼雜湊同步 (PHS) 與 Azure AD 密碼保護無關或不需要。
* 支援累加式部署，不過，只有在安裝網域控制站代理程式 (DC 代理程式) 的情況下，才會強制執行密碼原則。

## <a name="incremental-deployment"></a>累加式部署

Azure AD 密碼保護支援在 AD DS 網域中跨 Dc 進行累加部署。 請務必瞭解這真正的意義，以及取捨的意義。

Azure AD 的密碼保護 DC 代理程式軟體只能在安裝到 DC 時驗證密碼，而且只會驗證傳送至該 DC 的密碼變更。 您無法控制 Windows 用戶端電腦選擇哪些 Dc 來處理使用者密碼變更。 為了確保一致的行為和通用 Azure AD 密碼保護安全性強制執行，必須在網域中的所有 Dc 上安裝 DC 代理程式軟體。

許多組織都想要在完整部署之前，仔細測試其 Dc 子集的 Azure AD 密碼保護。 為了支援此案例，Azure AD 密碼保護支援部分部署。 即使網域中的其他 Dc 未安裝 DC 代理程式軟體，指定 DC 上的 DC 代理程式軟體仍會主動驗證密碼。 這種類型的部分部署不安全，因此不建議用於測試用途。

## <a name="architectural-diagram"></a>架構圖

在內部部署 AD DS 環境中部署 Azure AD 密碼保護之前，請務必瞭解基礎設計和函式概念。 下圖顯示 Azure AD 密碼保護的元件如何一起運作：

![Azure AD 密碼保護元件如何一起運作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 的密碼保護 Proxy 服務會在目前 AD DS 樹系中任何已加入網域的電腦上執行。 服務的主要目的是將密碼原則下載要求從 Dc 轉送到 Azure AD，然後將回應從 Azure AD 傳回至 DC。
* DC 代理程式的密碼篩選 DLL 會接收來自作業系統的使用者密碼驗證要求。 篩選器會將它們轉送到 dc 上本機執行的 DC 代理程式服務。
* Azure AD 密碼保護的 DC 代理程式服務會接收來自 DC 代理程式密碼篩選 DLL 的密碼驗證要求。 DC 代理程式服務會使用目前 (本機可用) 密碼原則來處理它們，並 *傳回通過* 或 *失敗*的結果。

## <a name="how-azure-ad-password-protection-works"></a>Azure AD 密碼保護的運作方式

內部部署 Azure AD 密碼保護元件的運作方式如下：

1. 每個 Azure AD 密碼保護 Proxy 服務實例會在 Active Directory 中建立 *serviceConnectionPoint* 物件，以向樹系中的 dc 通告本身。

    Azure AD 密碼保護的每個 DC 代理程式服務也會在 Active Directory 中建立 *serviceConnectionPoint* 物件。 此物件主要用於報告和診斷。

1. DC 代理程式服務負責起始從 Azure AD 下載新的密碼原則。 第一個步驟是藉由查詢樹系的 proxy *serviceConnectionPoint* 物件，找出 Azure AD 的密碼保護 Proxy 服務。

1. 找到可用的 proxy 服務時，DC 代理程式會將密碼原則下載要求傳送到 proxy 服務。 Proxy 服務接著會將要求傳送給 Azure AD，然後將回應傳回 DC 代理程式服務。

1. 當 DC 代理程式服務從 Azure AD 接收新的密碼原則之後，服務會將原則儲存在其網域 *sysvol* 資料夾共用的根目錄中的專用資料夾。 如果從網域中的其他 DC 代理程式服務複寫較新的原則，DC 代理程式服務也會監視此資料夾。

1. DC 代理程式服務一律會在服務啟動時要求新的原則。 DC 代理程式服務啟動之後，會每小時檢查目前本機可用原則的存在時間。 如果原則超過一小時，DC 代理程式會透過 proxy 服務向 Azure AD 要求新的原則，如先前所述。 如果目前的原則不超過一小時，則 DC 代理程式會繼續使用該原則。

1. 當 DC 收到密碼變更事件時，會使用快取的原則來判斷是否接受或拒絕新的密碼。

### <a name="key-considerations-and-features"></a>重要考慮和功能

* 每當下載 Azure AD 密碼保護密碼原則時，該原則就是租使用者專屬的原則。 換句話說，密碼原則一律是 Microsoft 全域禁用密碼清單和每個租使用者自訂禁用密碼清單的組合。
* DC 代理程式會透過 TCP 透過 RPC 與 proxy 服務通訊。 Proxy 服務會根據設定接聽動態或靜態 RPC 埠上的這些呼叫。
* DC 代理程式永遠不會在網路可用的埠上接聽。
* Proxy 服務永遠不會呼叫 DC 代理程式服務。
* Proxy 服務是無狀態的。 它永遠不會快取從 Azure 下載的原則或任何其他狀態。
* DC 代理程式服務一律會使用最新的本機可用密碼原則來評估使用者的密碼。 如果本機 DC 上沒有可用的密碼原則，則會自動接受密碼。 發生這種情況時，會記錄事件訊息以警告系統管理員。
* Azure AD 密碼保護不是即時原則應用程式引擎。 在 Azure AD 中進行密碼原則設定變更時，以及當該變更達到且在所有 Dc 上強制執行時，可能會有延遲。
* Azure AD 密碼保護作為現有 AD DS 密碼原則的補充，而不是取代。 這包括可能安裝的任何其他協力廠商密碼篩選 dll。 AD DS 一律需要所有密碼驗證元件同意，才能接受密碼。

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Azure AD 密碼保護的樹系/租使用者系結

在 AD DS 樹系中部署 Azure AD 密碼保護需要向 Azure AD 註冊該樹系。 每個部署的 proxy 服務也都必須向 Azure AD 註冊。 這些樹系和 proxy 註冊會與特定的 Azure AD 租使用者相關聯，該租使用者是由註冊期間使用的認證隱含識別。

樹系中的 AD DS 樹系和所有已部署的 proxy 服務都必須在相同的租使用者中註冊。 不支援在該樹系中有 AD DS 樹系或任何 proxy 服務註冊到不同的 Azure AD 租使用者。 這類錯誤設定部署的徵兆包括無法下載密碼原則。

> [!NOTE]
> 因此，擁有多個 Azure AD 租使用者的客戶必須選擇一個辨別租使用者來註冊每個樹系，以 Azure AD 密碼保護用途。

## <a name="download"></a>下載

您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)取得 Azure AD 密碼保護所需的兩個代理程式安裝程式。

## <a name="next-steps"></a>後續步驟

若要開始使用內部部署 Azure AD 密碼保護，請完成下列操作說明：

> [!div class="nextstepaction"]
> [部署內部部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
