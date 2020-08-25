---
title: Azure Active Directory Domain Services 概觀 | Microsoft Docs
description: 在此概觀中，您將了解 Azure Active Directory Domain Services 提供的內容，以及如何在您的組織中使用它來提供身分識別服務給雲端中的應用程式與服務。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 08/14/2020
ms.author: iainfou
ms.custom: contperfq1
ms.openlocfilehash: 2255f2193378d0cc3611680c6aa5f8e0837e99aa
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245171"
---
# <a name="what-is-azure-active-directory-domain-services"></a>什麼是 Azure Active Directory Domain Services？

Azure Active Directory Domain Services (AD DS) 提供受控網域服務，例如，網域加入、群組原則、輕量型目錄存取通訊協定 (LDAP) 與 Kerberos / NTLM 驗證。 您可以使用這些網域服務，而不需要在雲端部署、管理及修補網域控制站 (DC)。

Azure AD DS 受控網域可讓您在無法使用新式驗證方法的雲端中，或在不希望目錄查閱一律回到內部部署的 AD DS 環境中執行舊版應用程式。 您可以將這些舊版應用程式從內部部署環境隨即轉移到受控網域，而不需要管理雲端中的 AD DS 環境。

Azure AD DS 會與您現有的 Azure AD 租用戶整合。 此整合可讓使用者使用其現有的認證，登入已與受控網域連線的服務與應用程式。 您也可以使用現有的群組與使用者帳戶安全地存取資源。 這些功能可讓您更順暢地將內部部署資源隨即轉移至 Azure。

> [!div class="nextstepaction"]
> [若要開始使用，請使用 Azure 入口網站建立 Azure AD DS 受控網域][tutorial-create]

## <a name="how-does-azure-ad-ds-work"></a>Azure AD DS 如何運作？

當您建立 Azure AD DS 受控網域時，您可以定義唯一的命名空間。 這個命名空間是網域名稱，例如 aaddscontoso.com。 接著，會將兩個 Windows Server 網域控制站 (DC) 部署到您選取的 Azure 區域。 此 DC 的部署稱為「複本集」。

您不需要管理、設定或更新這些 DC。 Azure 平台會將 DC 當成受控網域的一部分來處理，包括備份。

此受控網域已設定為從 Azure AD 執行單向同步，以提供對一組集中式使用者、群組與認證的存取權。 您可以直接在此受控網域中建立資源，但系統不會將其同步處理回到 Azure AD。 Azure 中連線到受控網域的應用程式、服務與 VM 接著可以使用常見的 AD DS 功能，例如，網域加入、群組原則、LDAP 與 Kerberos / NTLM 驗證。

在具有內部部署 AD DS 環境的混合式環境中，[Azure AD Connect][azure-ad-connect] 會與受控網域同步身分識別資訊，該資訊會接著同步至 Azure AD DS。

![使用 AD Connect 讓 Azure AD Domain Services 與 Azure AD 和內部部署 AD DS 進行同步](./media/active-directory-domain-services-design-guide/sync-topology.png)

Azure AD DS 會從 Azure AD 複寫身分識別資訊，因此適用於僅限雲端的 Azure AD 租用戶，或與內部部署 AD DS 環境 同步處理的 Azure AD 租用戶。 這兩個環境都有一組相同的 Azure AD DS 功能。

* 如果您有現有的內部部署 AD DS 環境，您可以同步使用者帳戶資訊，為使用者提供一致的身分識別。 若要深入了解，請參閱[如何在受控網域中同步處理物件和認證][synchronization] \(部分機器翻譯\)。
* 針對僅限雲端環境，您不需要傳統內部部署 AD DS 環境，就能使用 Azure AD DS 的集中式識別服務。

您可以擴充受控網域，讓每個 Azure AD 租用戶都有一個以上的複本集。 您可以將複本集新增至任何支援 Azure AD DS 之 Azure 區域中的任何對等互連虛擬網路。 不同 Azure 區域中的其他複本集，可在 Azure 區域離線時，為舊版應用程式提供地區性的災害復原。 複本集目前為預覽狀態。 如需詳細資訊，請參閱[受控網域的複本集概念和功能][concepts-replica-sets]。

下列影片概述如何將 Azure AD DS 與您的應用程式和工作負載整合，以在雲端提供識別服務：

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

若要查看作用中 Azure AD DS 部署案例，您可以瀏覽下列範例：

* [混合式組織的 Azure AD DS](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [僅限雲端的組織的 Azure AD DS](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS 的功能與優點

為了為雲端中的應用程式與 VM 提供識別服務，Azure AD DS 完全相容於傳統 AD DS 環境的作業，例如，網域加入、安全 LDAP (LDAPS)、群組原則、DNS 管理，以及 LDAP 繫結和讀取支援。 LDAP 寫入支援可供在受控網域中建立的物件使用，但不可供從 Azure AD 同步的資源使用。

若要深入了解您的身分識別選項，請[比較 Azure AD DS 與 Azure AD、Azure VM 上的 AD DS，以及 AD DS 內部部署][compare]。

Azure AD DS 的下列功能簡化了部署與管理作業：

* **簡化的部署體驗：** 使用 Azure 入口網站中的單一精靈為您的 Azure AD 租用戶啟用 Azure AD DS。
* **與 Azure AD 整合：** 使用者帳戶、群組成員資格與認證，都自動可從您的 Azure AD 租用戶取得。 新使用者、群組，或 Azure AD 租用戶或內部部署 AD DS 環境中的屬性變更，都會自動同步到 Azure AD DS。
    * 外部目錄中連結至 Azure AD 的帳戶無法在 Azure AD DS 中使用。 這些外部目錄無法使用認證，因此無法同步處理到受控網域。
* **使用公司認證/密碼：** Azure AD DS 中使用者的密碼與您 Azure AD 租用戶中使用者的密碼相同。 使用者可以使用其公司認證來將機器加入網域，以互動方式或透過遠端桌面登入，以及對受控網域進行驗證。
* **NTLM 和 Kerberos 驗證：** 利用對 NTLM 與 Kerberos 驗證的支援，您就能部署依賴 Windows 整合式驗證的應用程式。
* **高可用性：** Azure AD DS 包含多個網域控制站，為您的受控網域提供高可用性。 此高可用性可保證服務執行時間，且可從失敗復原。
    * 在支援 [Azure 可用性區域][availability-zones]的區域中，這些網域控制站也會分散到多個區域，以提高復原能力。
    * [複本集][concepts-replica-sets] 也可在 Azure 區域離線時，為舊版應用程式提供地區性的災害復原。

受控網域的一些關鍵層面如下：

* 受控網域是獨立網域。 它不是內部部署網域的延伸。
    * 如有需要，您可以建立從 Azure AD DS 到內部部署 AD DS 環境的單向輸出樹系信任。 如需詳細資訊，請參閱 [Azure AD DS 的資源樹系概念和功能][ forest-trusts]。
* 您的 IT 小組不需要管理、修補或監視此受控網域的網域控制站。

針對執行 AD DS 內部部署的混合式環境，您不需要管理對受控網域的 AD 複寫。 使用者帳戶、群組成員資格與來自您內部部署目錄的認證會透過 [Azure AD Connect][azure-ad-connect] 同步到 Azure AD。 在受控網域中會自動提供這些使用者帳戶、群組成員資格和認證。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure AD DS 與其身分識別解決方案的比較，以及同步的運作方式，請參閱下列文章：

* [比較 Azure AD DS 與 Azure AD、Azure VM 上的 Active Directory Domain Services，以及 Active Directory Domain Services 內部部署][compare] \(英文\)
* [了解 Azure AD Domain Services 如何與 Azure AD 目錄同步處理][synchronization]
* 若要了解如何管理受控網域，請參閱 [Azure AD DS 中的使用者帳戶、密碼和管理的管理概念][administration-concepts] \(部分機器翻譯\)。

若要開始使用，請[使用 Azure 入口網站建立受控網域][tutorial-create]。

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
