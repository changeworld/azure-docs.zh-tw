---
title: 適用於 Azure AD Domain Services 的資源樹系概念 | Microsoft Docs
description: 了解 Azure Active Directory Domain Services 中的資源樹系，以及其在混合式環境中如何使用有限的使用者驗證選項或安全性考慮來讓組織受益。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e0e5dde246dbcd5e5cb2e4ae923872a59a539d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476413"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 的資源樹系概念及功能

Azure Active Directory Domain Services (AD DS) 提供舊版、內部部署、企業營運系統應用程式的登入體驗。 內部部署和雲端使用者的使用者、群組和密碼雜湊都會同步處理到 Azure AD DS 受控網域。 這些已同步處理的密碼雜湊會提供使用者一組認證，其可用於內部部署 AD DS、Office 365 和 Azure Active Directory。

雖然這些使用者密碼雜湊能夠保護並提供額外的安全性優勢，但有些組織卻無法將其同步處理至 Azure AD 或 Azure AD DS。 組織中的使用者因為只使用智慧卡驗證，所以不知道自己的密碼。 這些限制讓有些組織無法使用 Azure AD DS 將內部部署傳統應用程式隨即轉移至 Azure。

為解決這些需求和限制，您可建立使用資源樹系的 Azure AD DS 受控網域。 這篇概念性文章會說明何謂樹系，以及其如何信任其他資源以提供安全的驗證方法。 Azure AD DS 資源樹系目前為預覽狀態。

> [!IMPORTANT]
> Azure AD DS 資源樹系目前不支援 Azure HDInsight 或 Azure 檔案儲存體。 預設的 Azure AD DS 使用者樹系支援這兩項額外的服務。

## <a name="what-are-forests"></a>樹系是什麼？

「樹系」是 Active Directory Domain Services (AD DS) 用以分組一或多個「網域」的邏輯建構。 然後，這些網域就會儲存使用者或群組的物件，並提供驗證服務。

在 Azure AD DS 中，樹系僅包含一個網域。 但內部部署 AD DS 樹系通常會包含許多網域。 在大型組織中，特別是併購後，會有多個內部部署樹系，每個樹系又包含多個網域。

根據預設，Azure AD DS 受控網域會建立為「使用者」樹系。 這種類型的樹系會同步 Azure AD 中的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。 使用者帳戶可直接驗證 Azure AD DS 受控網域，例如登入已加入網域的 VM。 當可同步處理密碼雜湊，但使用者不使用獨佔式登入方法 (例如智慧卡驗證) 時，使用者樹系即可發揮功能。

在 Azure AD DS 的「資源」樹系中，使用者會透過其內部部署 AD DS 的單向樹系「信任」驗證。 使用此方法，使用者物件和密碼雜湊不會同步處理至 Azure AD DS。 使用者物件和認證僅存在於內部部署 AD DS。 這種方法可讓企業裝載 Azure 中依賴 LDAPS、Kerberos 或 NTLM 等傳統驗證的資源和應用程式平台，但會移除所有驗證問題或顧慮。 Azure AD DS 資源樹系目前為預覽狀態。

資源樹系也提供一次隨即轉移一個應用程式元件的功能。 許多舊版內部部署應用程式都是多層式的，通常使用網頁伺服器或前端加上許多與資料庫相關的元件。 這些層級使得難以使用一個步驟來將整個應用程式隨即轉移到雲端。 利用資源樹系，您可分階段將應用程式提升至雲端，其可供輕鬆地將應用程式移至 Azure。

## <a name="what-are-trusts"></a>信任是什麼？

具有一個以上網域的組織通常需要使用者在不同網域中存取共用資源。 存取這些共用資源需要使用者在一個網域中向另一個網域驗證。 為將這些驗證和授權功能提供給不同網域的用戶端和伺服器，這兩個網域之間必須有「信任」。

具有網域信任，每個網域的驗證機制就可信任來自另一個網域其驗證。 信任透過驗證連入驗證要求是否來自信任授權 (「信任的」網域)，以協助控制對資源網域 (「要信任的」網域) 中共用資源的存取。 信任的作用是銜接不同網域，以只允許通過驗證的驗證要求往來於網域之間。

信任如何通過驗證要求取決於其設定方式。 信任的設定方式有下列幾種：

* **單向**：從信任網域存取要信任網域的資源。
* **雙向**：每個網域都可存取其他網域的資源。

信任也可設定為以下列其中一項方式來處理其他信任關係：

* **不可轉移**：信任只存在於兩個信任夥伴的網域之間。
* **可轉移**：信任會自動擴展至任一夥伴信任的任何其他網域。

在某些情況下，建立網域時即會自動建立信任關係。 其他時候，您必須選擇一種信任類型，並明確建立適當的關聯性。 Active Directory 目錄服務其組織方式決定了所用信任特定類型和這些信任關係的結構，以及不同版本的 Windows 能否在網路上共存。

## <a name="trusts-between-two-forests"></a>兩個樹系之間的信任

您可手動建立單向或雙向樹系信任，以將單一樹系中的網域信任擴展至另一個樹系。 樹系信任是僅存在於樹系根網域和第二樹系根網域之間的可轉移信任。

* 單向樹系信任允許一個樹系中的所有使用者信任其他樹系中的所有網域。
* 雙向樹系信任則會形成兩個樹系中，每個網域之間都是可轉移的信任關係。

樹系信任的轉移能力僅限於兩個樹系合作夥伴。 樹系信任不會擴展到這兩個合作夥伴中任一個信任的其他樹系。

![從 Azure AD DS 到內部部署 AD DS 的樹系信任圖表](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

您可根據組織的 Active Directory 結構來建立不同網域和樹系信任設定。 Azure AD DS 僅支援單向樹系信任。 在此設定中，Azure AD DS 中資源可信任內部部署樹系中的所有網域。

## <a name="supporting-technology-for-trusts"></a>支援信任的技術

信任會使用各種服務和功能，例如使用 DNS 來尋找合作樹系中的網域控制站。 信任也依賴 NTLM 和 Kerberos 驗證通訊協定以及 Windows 授權和存取控制機制，以協助在 Active Directory 網域和樹系之間提供安全的通訊基礎結構。 下列服務和功能協助支援成功的信任關係。

### <a name="dns"></a>DNS

AD DS 需要使用 DNS 定位和命名網域控制站 (DC)。 DNS 提供下列支援，其可讓 AD DS 成功運作：

* 名稱解析服務，其可讓網路主機和服務找到 DC。
* 命名結構，其可讓企業在目錄服務網域的名稱中反映組織結構。

通常會部署的 DNS 網域命名空間，其可鏡像 AD DS 網域命名空間。 如果在部署 AD DS 之前即有 DNS 命名空間，則一般會針對 Active Directory 分割此 DNS 命名空間，並為 Active Directory 樹系根目錄建立 DNS 子網域和委派。 接著，為每個 Active Directory 子網域新增額外的 DNS 網域名稱。

DNS 也用於支援定位 Active Directory DC。 DNS 區域會填入 DNS 資源記錄，讓網路主機和服務找出 Active Directory DC。

### <a name="applications-and-net-logon"></a>應用程式和 Net Logon

應用程式和 Net Logon 服務都是 Windows 分散式安全性通道模型的元件。 與 Windows Server 和 Active Directory 整合的應用程式會使用驗證通訊協定與 Net Logon 服務通訊，以建立可供執行驗證的安全路徑。

### <a name="authentication-protocols"></a>驗證通訊協定

Active Directory DC 使用下列其中一項通訊協定來驗證使用者和應用程式：

* **Kerberos 第 5 版驗證通訊協定**
    * Kerberos 第5版通訊協定是執行 Windows 及支援協力廠商作業系統的內部部署電腦所使用預設驗證通訊協定。 此通訊協定是在 RFC 1510 中指定，且已與後列項目完全整合：Active Directory、伺服器訊息區 (SMB)、HTTP 和遠端程序呼叫 (RPC)，以及使用這些通訊協定的用戶端和伺服器應用程式。
    * 使用 Kerberos 通訊協定時，伺服器不必連絡 DC。 而是由用戶端向伺服器帳戶網域中 DC 提出要求來取得伺服器的票證。 然後，伺服器會驗證票證，而不用諮詢任何其他授權單位。
    * 如有任何一部交易相關電腦不支援 Kerberos 第 5 版通訊協定，則使用 NTLM 通訊協定。

* **NTLM 驗證通訊協定**
    * NTLM 通訊協定是舊版作業系統使用的傳統網路驗證通訊協定。 基於相容性，Active Directory 網域會使用此通訊協定以處理來自專為舊版 Windows 用戶端和伺服器以及協力廠商作業系統所設計應用程式的網路驗證要求。
    * 在用戶端與伺服器之間使用 NTLM 通訊協定時，伺服器必須連絡 DC 上的網域驗證服務，以驗證用戶端認證。 伺服器會將用戶端認證轉送至用戶端帳戶網域中的 DC，以驗證用戶端。
    * 當兩個 Active Directory 網域或樹系透過信任連線後，就會路由使用這些通訊協定所提出的驗證要求，以存取這兩個樹系中的資源。

## <a name="authorization-and-access-control"></a>授權和存取控制

授權和信任技術共同合作，在 Active Directory 網域或樹系之間提供安全的通訊基礎結構。 授權會決定使用者對網域中資源所擁有的存取層級。 信任會提供一個路徑來驗證其他網域中的使用者，使其對這些網域中的共用資源要求可獲得授權，藉此促進跨網域的使用者授權。

當信任網域要驗證信任網域所提出的驗證要求時，此要求會被傳遞至目標資源。 然後，目標資源會根據其存取控制設定來決定是否授權由信任網域的使用者、服務或電腦所提出特定要求。

信任提供此機制來驗證傳遞至信任網域的驗證要求。 資源電腦上存取控制機制會決定授與信任網域中要求者的最終存取層級。

## <a name="next-steps"></a>後續步驟

若要深入了解信任，請參閱[樹系信任如何在 Azure AD DS 中工作？][concepts-trust]

若要開始建立具有資源樹系的 Azure AD DS 受控網域，請參閱[建立與設定 Azure AD DS 受控網域][tutorial-create-advanced]。 接著，您可[建立內部部署網域的輸出樹系信任 (預覽)][create-forest-trust]。

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
