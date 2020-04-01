---
title: Azure AD 域服務的資源林概念 |微軟文件
description: 瞭解 Azure 活動目錄域服務中的資源林是什麼,以及它們如何在具有有限使用者身份驗證選項或安全問題的混合環境中使組織受益。
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
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476413"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure 活動目錄網域服務的資源林概念和功能

Azure 活動目錄域服務 (AD DS) 為舊版本地業務線應用程式提供登錄體驗。 本地和雲端用戶的使用者、組和密碼哈希將同步到 Azure AD DS 託管域。 這些同步的密碼哈希是為使用者提供可用於本地 AD DS、Office 365 和 Azure 活動目錄的單個認證的集的原因。

儘管安全並提供其他安全優勢,但某些組織無法將這些用戶密碼哈希同步到 Azure AD 或 Azure AD DS。 組織中的使用者可能不知道其密碼,因為他們只使用智慧卡身份驗證。 這些限制阻止某些組織使用 Azure AD DS 提升本地經典應用程式並將其轉移到 Azure。

為了滿足這些需求和限制,您可以創建使用資源林的 Azure AD DS 託管域。 本文的概念文章解釋了什麼是林,以及它們如何信任其他資源來提供安全的身份驗證方法。 Azure AD DS 資源林目前處於預覽狀態。

> [!IMPORTANT]
> Azure AD DS 資源林目前不支援 Azure HDInsight 或 Azure 檔。 預設的 Azure AD DS 使用者林確實支援這兩種附加服務。

## <a name="what-are-forests"></a>什麼是森林?

*林*是活動目錄域服務 (AD DS) 用於對一個或多個*域*進行分組的邏輯構造。 然後,域為使用者或組存儲物件,並提供身份驗證服務。

在 Azure AD DS 中,林只包含一個域。 本地 AD DS 林通常包含許多域。 在大型組織中,尤其是在合併和收購之後,您最終可能會擁有多個本地林,每個林都包含多個域。

默認情況下,Azure AD DS 託管域將作為*使用者*林創建。 這種類型的樹系會同步 Azure AD 中的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。 使用者帳戶可以直接針對 Azure AD DS 託管域進行身份驗證,例如登錄到加入網域的 VM。 當使用者林可以同步密碼哈希,並且使用者不使用專用登錄方法(如智慧卡身份驗證)時,使用者林將起作用。

在 Azure AD DS*資源*林中,用戶透過本地 AD DS 的單向林*信任*進行身份驗證。 使用此方法,使用者物件和密碼哈希不會同步到 Azure AD DS。 使用者物件和憑據僅存在於本地 AD DS 中。 此方法允許企業在 Azure 中託管依賴於經典身份驗證(如 LDAPS、Kerberos 或 NTLM)的資源和應用程式平臺,但刪除了任何身份驗證問題或疑慮。 Azure AD DS 資源林目前處於預覽狀態。

資源林還提供一次提升和移動應用程式一個元件的功能。 許多舊式本地應用程式是多層的,通常使用 Web 伺服器或前端和許多與資料庫相關的元件。 這些層使得很難一步提升整個應用程式並將其轉移到雲中。 使用資源林,可以分階段將應用程式提升到雲中,從而更輕鬆地將應用程序移動到 Azure。

## <a name="what-are-trusts"></a>什麼是信任?

具有多個域的組織通常需要使用者訪問不同域中的共享資源。 訪問這些共用資源需要一個域中的使用者對另一個域進行身份驗證。 要在不同域中的用戶端和伺服器之間提供這些身份驗證和授權功能,必須在*兩個域*之間建立信任。

使用域信任,每個域的身份驗證機制信任來自另一個域的身份驗證。 信任透過驗證傳入認證要求是否來自受信任的機構(*受信任的*網域),説明提供對資源域(*信任*網域)中的分享資源的受控存取。 信任充當僅允許經過驗證的身份驗證請求在域之間移動的橋樑。

信任如何傳遞身份驗證請求取決於其配置方式。 信任可以透過以下方式的一個設定:

* **單向**- 提供從受信任域到信任域中的資源的訪問許可權。
* **雙向**- 提供從每個域對其他域中的資源的訪問。

信任並設定為以下欄方式之一處理其他信任關係:

* **非傳遞 -** 信任僅存在於兩個信任夥伴域之間。
* **傳遞 -** 信任會自動擴展到任一合作夥伴信任的任何其他域。

在某些情況下,在創建域時會自動建立信任關係。 其他時候,您必須選擇信任類型並顯式建立適當的關係。 所使用的信任的特定類型以及這些信任關係的結構取決於 Active Directory 目錄服務的組織方式,以及不同版本的 Windows 在網路上是否共存。

## <a name="trusts-between-two-forests"></a>兩個林之間的信任

您可以透過手動建立單向或雙向林信任將單個林中的域信任擴展到另一個林。 林信任是僅存在於林根域和第二個林根域之間的傳遞信任。

* 單向林信任允許一個林中的所有使用者信任另一個林中的所有域。
* 雙向林信任在兩個林中的每個域之間形成傳遞信任關係。

森林信託的過境性僅限於兩個森林夥伴。 林信任不會擴展到任一合作夥伴信任的其他林。

![從 Azure AD DS 到本地端 AD DS 的林信任圖](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

您可以根據組織的活動目錄結構創建不同的域和林信任配置。 Azure AD DS 僅支援單向林信任。 在此配置中,Azure AD DS 中的資源可以信任本地林中的所有域。

## <a name="supporting-technology-for-trusts"></a>支援信託技術

信任使用各種服務和功能(如 DNS)在合作林中定位域控制器。 信任還依賴於 NTLM 和 Kerberos 身份驗證協定以及基於 Windows 的授權和存取控制機制,以説明跨 Active Directory 功能和林提供安全的通訊基礎結構。 以下服務和功能有助於支援成功的信任關係。

### <a name="dns"></a>DNS

AD DS 需要 DNS 進行域控制器 (DC) 位置和命名。 DNS 提供以下支援,使 AD DS 正常工作:

* 允許網路主機和服務定位 DC 的名稱解析服務。
* 使企業能夠在其目錄服務域的名稱中反映其組織結構的命名結構。

通常部署一個 DNS 域名域域空間,該域域域空間反映了 AD DS 功能變數名稱域。 如果在 AD DS 部署之前存在現有的 DNS 命名空間,則通常會為 Active Directory 分區 DNS 命名空間,並為 Active Directory 林根創建 DNS 子域和委派。 然後為每個活動目錄子域添加其他 DNS 功能變數名稱。

DNS 還用於支援活動目錄 DC 的位置。 DNS 區域填充了 DNS 資源記錄,這些記錄使網路主機和服務能夠定位活動目錄 DC。

### <a name="applications-and-net-logon"></a>應用程式和淨登錄

應用程式和 Net 登入服務都是 Windows 分散式安全通道模型的元件。 與 Windows 伺服器和 Active Directory 整合的應用程式使用身份驗證協定與 Net Logon 服務通訊,以便可以建立可以進行身份驗證的安全路徑。

### <a name="authentication-protocols"></a>驗證通訊協定

活動目錄 DC 使用以下協定之一對使用者和應用程式進行身份驗證:

* **Kerberos 版本 5 認證協定**
    * Kerberos 版本 5 協定是運行 Windows 和支援第三方作業系統的本地電腦使用的預設身份驗證協定。 該協定在 RFC 1510 中指定,並與 Active Directory、伺服器訊息區塊 (SMB)、HTTP 和遠端過程呼叫 (RPC)以及使用這些協定的用戶端和伺服器應用程式完全整合。
    * 使用 Kerberos 協定時,伺服器不必與 DC 聯繫。 相反,客戶端通過從伺服器帳戶域中的 DC 請求伺服器票證來獲取伺服器票證。 然後,伺服器無需諮詢任何其他機構即可驗證票證。
    * 如果事務中涉及的任何電腦不支援 Kerberos 版本 5 協定,則使用 NTLM 協定。

* **NTLM 驗證通訊協定**
    * NTLM 協定是舊操作系統使用的經典網路身份驗證協定。 出於相容性原因,Active Directory 功能使用它來處理來自早期基於 Windows 的用戶端和伺服器以及第三方作業系統的應用程式的網路身份驗證請求。
    * 在用戶端和伺服器之間使用NTLM協定時,伺服器必須聯繫DC上的網域身份驗證服務以驗證客戶端認證。 伺服器通過將用戶端憑據轉發到用戶端帳戶域中的 DC 來驗證用戶端。
    * 當兩個活動目錄域或林由信任連接時,可以使用這些協議進行的身份驗證請求進行路由,以提供對兩個林中的資源的訪問。

## <a name="authorization-and-access-control"></a>授權和存取控制

授權和信任技術協同工作,跨 Active Directory 域或林提供安全的通信基礎結構。 授權確定使用者對域中的資源具有的訪問級別。 信任通過提供一條用於驗證其他域中使用者的路徑,以便授權使用者對這些域中共用資源的請求,從而促進使用者的跨域授權。

當受信任域中所做的身份驗證請求由受信任的域驗證時,它將傳遞給目標資源。 然後,目標資源根據存取控制配置確定是否授權受信任網域中的使用者、服務或電腦發出的特定請求。

信任提供此機制來驗證傳遞到信任域的身份驗證請求。 資源計算機上的訪問控制機制確定授予受信任域中的請求者的最終訪問級別。

## <a name="next-steps"></a>後續步驟

要瞭解有關信任的更多詳細資訊,請參閱[林信任如何在 Azure AD DS 中工作?][concepts-trust]

要開始使用資源林建立 Azure AD DS 託管域,請參閱[建立和設定 Azure AD DS 託管域][tutorial-create-advanced]。 然後,您可以[創建到本地域的出站林信任(預覽)。][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
