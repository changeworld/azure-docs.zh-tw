---
title: Azure AD Domain Services 的資源樹系概念 |Microsoft Docs
description: 瞭解資源樹系的 Azure Active Directory Domain Services，以及如何在混合式環境中使用有限的使用者驗證選項或安全性考慮，讓您的組織受益。
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
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476413"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 的資源樹系概念和功能

Azure Active Directory Domain Services （AD DS）提供適用于舊版、內部部署、企業營運應用程式的登入體驗。 內部部署和雲端使用者的使用者、群組和密碼雜湊會同步處理到 Azure AD DS 受控網域。 這些已同步處理的密碼雜湊會提供使用者一組可用於內部部署 AD DS、Office 365 和 Azure Active Directory 的認證。

雖然保護並提供額外的安全性優點，但某些組織無法將這些使用者密碼雜湊同步處理至 Azure AD 或 Azure AD DS。 組織中的使用者可能不知道他們的密碼，因為他們只會使用智慧卡驗證。 這些限制可防止某些組織使用 Azure AD DS 將內部部署傳統應用程式隨即轉移至 Azure。

若要解決這些需求和限制，您可以建立使用資源樹系的 Azure AD DS 受控網域。 這篇概念性文章說明哪些是樹系，以及它們如何信任其他資源來提供安全的驗證方法。 Azure AD DS 資源樹系目前為預覽狀態。

> [!IMPORTANT]
> Azure AD DS 資源樹系目前不支援 Azure HDInsight 或 Azure 檔案儲存體。 預設 Azure AD DS 使用者樹系支援這兩個額外的服務。

## <a name="what-are-forests"></a>什麼是樹系？

*樹*系是 Active Directory Domain Services （AD DS）用來將一或多個*網域*分組的邏輯結構。 然後，網域會儲存使用者或群組的物件，並提供驗證服務。

在 Azure AD DS 中，樹系僅包含一個網域。 內部部署 AD DS 樹系通常包含許多網域。 在大型組織中，尤其是在合併和收購之後，您最後可能會有多個內部部署樹系，每個樹系都包含多個網域。

根據預設，系統會將 Azure AD DS 受控網域建立為*使用者*樹系。 這種類型的樹系會同步 Azure AD 中的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。 使用者帳戶可以直接針對 Azure AD DS 受控網域進行驗證，例如登入已加入網域的 VM。 使用者樹系適用于可以同步處理密碼雜湊，而使用者不使用獨佔式登入方法（例如智慧卡驗證）的情況。

在 Azure AD DS*資源*樹系中，使用者會從其內部部署 AD DS 對單向樹系*信任*進行驗證。 使用此方法時，使用者物件和密碼雜湊不會同步處理至 Azure AD DS。 使用者物件和認證僅存在於內部部署 AD DS。 這種方法可讓企業裝載 Azure 中的資源和應用程式平臺，而這些是依賴傳統驗證（例如 LDAPS、Kerberos 或 NTLM），但會移除任何驗證問題或考慮。 Azure AD DS 資源樹系目前為預覽狀態。

資源樹系也提供一次將應用程式隨即轉移一個元件的功能。 許多舊版內部部署應用程式都是多層式的，通常使用 web 伺服器或前端，以及許多資料庫相關的元件。 這些層級讓整個應用程式難以隨即轉移到雲端，只需要一個步驟即可。 透過資源樹系，您可以分階段方式將應用程式提升至雲端，讓您更輕鬆地將應用程式移至 Azure。

## <a name="what-are-trusts"></a>什麼是信任？

具有一個以上網域的組織通常需要使用者存取不同網域中的共用資源。 若要存取這些共用資源，使用者必須在一個網域中驗證另一個網域。 若要在不同網域中的用戶端與伺服器之間提供這些驗證和授權功能，這兩個網域之間必須有*信任關係*。

使用網域信任時，每個網域的驗證機制會信任來自另一個網域的驗證。 信任有助於透過驗證傳入驗證要求是否來自受信任的授權單位（*受信任*的網域），藉此提供對資源網域（*信任*網域）中共用資源的控制存取。 信任的作用是只允許已驗證的驗證要求在網域之間移動的橋接器。

信任通過驗證要求的方式，取決於其設定方式。 您可以透過下列其中一種方式來設定信任：

* **單向-提供**從受信任網域到信任網域中資源的存取權。
* **雙向**-提供從每個網域到另一個網域中資源的存取權。

信任也會設定為以下列其中一種方式處理其他信任關係：

* 非**轉移**-信任只存在於兩個信任夥伴網域之間。
* 可**轉移**-信任會自動延伸至任一夥伴信任的任何其他網域。

在某些情況下，建立網域時，會自動建立信任關係。 其他時候，您必須選擇一種信任類型，並明確地建立適當的關聯性。 使用的特定信任類型以及這些信任關係的結構，取決於如何組織 Active Directory 目錄服務，以及是否在網路上共存不同版本的 Windows。

## <a name="trusts-between-two-forests"></a>兩個樹系之間的信任

您可以手動建立單向或雙向樹系信任，以將單一樹系中的網域信任延伸至另一個樹系。 樹系信任是只存在於樹系根域和第二個樹系根域之間的可轉移信任。

* 單向樹系信任可讓一個樹系中的所有使用者信任另一個樹系中的所有網域。
* 雙向樹系信任會形成兩個樹系中每個網域之間可轉移的信任關係。

樹系信任的轉移能力僅限於這兩個樹系合作夥伴。 樹系信任不會延伸到任何合作夥伴所信任的其他樹系。

![從 Azure AD DS 到內部部署 AD DS 的樹系信任圖表](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

您可以根據組織的 Active Directory 結構，建立不同的網域和樹系信任設定。 Azure AD DS 僅支援單向樹系信任。 在此設定中，Azure AD DS 中的資源可以信任內部部署樹系中的所有網域。

## <a name="supporting-technology-for-trusts"></a>信任的支援技術

信任會使用各種服務和功能，例如 DNS 來尋找合作樹系中的網域控制站。 信任也取決於 NTLM 和 Kerberos 驗證通訊協定，以及 Windows 型授權和存取控制機制，以協助在 Active Directory 網域和樹系之間提供安全的通訊基礎結構。 下列服務和功能有助於支援成功的信任關係。

### <a name="dns"></a>DNS

AD DS 需要 DNS 來進行網域控制站（DC）位置和命名。 下列 DNS 支援是為了讓 AD DS 能夠順利工作而提供：

* 可讓網路主機和服務尋找 Dc 的名稱解析服務。
* 一種命名結構，可讓企業在其目錄服務網域的名稱中反映其組織結構。

通常會部署 DNS 網域命名空間，以鏡像 AD DS 網域命名空間。 如果 AD DS 部署之前有現有的 DNS 命名空間，則 DNS 命名空間通常會針對 Active Directory 進行分割，並建立 Active Directory 樹系根目錄的 DNS 子域和委派。 接著，會為每個 Active Directory 子域新增額外的 DNS 功能變數名稱。

DNS 也用來支援 Active Directory Dc 的位置。 DNS 區域會填入 DNS 資源記錄，讓網路主機和服務找出 Active Directory Dc。

### <a name="applications-and-net-logon"></a>應用程式和網路登入

應用程式和 Net Logon 服務都是 Windows 分散式安全性通道模型的元件。 與 Windows Server 和 Active Directory 整合的應用程式會使用驗證通訊協定與 Net Logon 服務進行通訊，因此可以建立安全的路徑來進行驗證。

### <a name="authentication-protocols"></a>驗證通訊協定

Active Directory Dc 會使用下列其中一種通訊協定來驗證使用者和應用程式：

* **Kerberos 第5版驗證通訊協定**
    * Kerberos 第5版通訊協定是執行 Windows 及支援協力廠商作業系統的內部部署電腦所使用的預設驗證通訊協定。 此通訊協定是在 RFC 1510 中指定，並且與 Active Directory、伺服器訊息區（SMB）、HTTP 和遠端程序呼叫（RPC），以及使用這些通訊協定的用戶端和伺服器應用程式完全整合。
    * 使用 Kerberos 通訊協定時，伺服器不需要與 DC 聯繫。 相反地，用戶端會向伺服器帳戶網域中的 DC 要求一個票證，以取得伺服器的票證。 伺服器接著會驗證票證，而不會諮詢任何其他授權單位。
    * 如果交易中涉及的任何電腦不支援 Kerberos 第5版通訊協定，則會使用 NTLM 通訊協定。

* **NTLM 驗證通訊協定**
    * NTLM 通訊協定是舊版作業系統所使用的傳統網路驗證通訊協定。 基於相容性的理由，Active Directory 網域會使用它來處理來自針對舊版 Windows 用戶端和伺服器和協力廠商作業系統所設計之應用程式的網路驗證要求。
    * 在用戶端與伺服器之間使用 NTLM 通訊協定時，伺服器必須連線到 DC 上的網域驗證服務，以確認用戶端認證。 伺服器會藉由將用戶端認證轉送至用戶端帳戶網域中的 DC 來驗證用戶端。
    * 當兩個 Active Directory 網域或樹系由信任連接時，會路由使用這些通訊協定所提出的驗證要求，以提供這兩個樹系中資源的存取權。

## <a name="authorization-and-access-control"></a>授權和存取控制

授權和信任技術共同合作，在 Active Directory 網域或樹系之間提供安全的通訊基礎結構。 授權會決定使用者對網域中的資源所擁有的存取層級。 信任會提供一個路徑來驗證其他網域中的使用者，讓他們對這些網域中共用資源的要求可以獲得授權，藉此促進跨網域的使用者授權。

當受信任網域驗證在信任網域中提出的驗證要求時，會將它傳遞至目標資源。 然後，目標資源會根據其存取控制設定，決定是否要授權由受信任網域中的使用者、服務或電腦所提出的特定要求。

信任提供此機制來驗證傳遞至信任網域的驗證要求。 資源電腦上的存取控制機制會決定授與受信任網域中要求者的最終存取層級。

## <a name="next-steps"></a>後續步驟

若要深入瞭解信任，請參閱[樹系信任如何在 AZURE AD DS 中工作？][concepts-trust]

若要開始建立具有資源樹系的 Azure AD DS 受控網域，請參閱[建立和設定 AZURE AD ds 受控網域][tutorial-create-advanced]。 接著，您可以對內部[部署網域（預覽）建立輸出樹系信任][create-forest-trust]。

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
