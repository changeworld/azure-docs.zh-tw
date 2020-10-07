---
title: 比較 Azure 中以 Active Directory 為基礎的服務 | Microsoft Docs
description: 在此概觀中，您會比較 Active Directory Domain Services、Azure Active Directory 和 Azure Active Directory Domain Services 的不同身分識別供應項目。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: iainfou
ms.openlocfilehash: af4ebcac38d4714b8a133087fe328b4d2f6c4872
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396360"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>比較自我管理 Active Directory Domain Services、Azure Active Directory 和受控 Azure Active Directory Domain Services

若要對中央身分識別提供應用程式、服務或裝置存取權，有三種常見的方式可在 Azure 中使用以 Active Directory 為基礎的服務。 透過身分識別解決方案的這些選擇，您能夠彈性地依據組織需求來使用最適當的目錄。 例如，如果您主要管理執行行動裝置的僅限雲端使用者，那麼建立並執行您自己的 Active Directory Domain Services (AD DS) 身分識別解決方案可能就沒有意義。 相反地，您可以直接使用 Azure Active Directory。

雖然三個以 Active Directory 為基礎的身分識別解決方案共用共同的名稱和技術，但其設計訴求是提供符合不同客戶需求的服務。 概括地說，這些身分識別解決方案和功能集為：

* **Active Directory Domain Services (AD DS)** - 符合企業需求的輕量型目錄存取通訊協定 (LDAP) 伺服器，提供身分識別及驗證、電腦物件管理、群組原則和信任等主要功能。
    * AD DS 在許多具有內部部署 IT 環境的組織中是中央元件，並提供核心使用者帳戶驗證和電腦管理功能。
    * 如需詳細資訊，請參閱 [Windows 伺服器文件中的 Active Directory Domain Services 概觀][overview-adds]。
* **Azure Active Directory (Azure AD)** - 以雲端為基礎的身分識別和行動裝置管理，可為 Microsoft 365、Azure 入口網站或 SaaS 應用程式等資源提供使用者帳戶和驗證服務。
    * Azure AD 可以與內部部署 AD DS 環境同步，為雲端中以原生方式運作的使用者提供單一身分識別。
    * 如需 Azure AD 的詳細資訊，請參閱[什麼是Azure Active Directory？][whatis-azuread]
* **Azure Active Directory Domain Services (Azure AD DS)** - 提供受控網域服務，其中有一小部分能與傳統 AD DS 功能完全相容，例如，網域加入、群組原則、LDAP 和 Kerberos / NTLM 驗證等功能。
    * Azure AD DS 與 Azure AD 整合，其本身可與內部部署 AD DS 環境進行同步處理。 這項功能可將中央身分識別使用案例延伸到在 Azure 中執行的傳統 Web 應用程式，作為隨即轉移策略的一部分。
    * 若要深入了解 Azure AD 與內部部署的同步處理，請參閱[受控網域中的物件和認證如何同步][synchronization]。

此概觀文章會比較和對比這些身分識別解決方案搭配使用或個別使用的方式 (視組織的需求而定)。

> [!div class="nextstepaction"]
> [若要開始使用，請使用 Azure 入口網站建立 Azure AD DS 受控網域][tutorial-create]

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS 和自我管理 AD DS

如果您有需要存取傳統驗證機制 (例如 Kerberos 或 NTLM) 的應用程式和服務，有兩種方式可以在雲端中提供 Active Directory Domain Services：

* 使用 Azure Active Directory Domain Services (Azure AD DS) 建立的「受控網域」。 Microsoft 會建立並管理所需的資源。
* 使用傳統資源 (例如虛擬機器 (VM)、Windows Server 客體 OS 和 Active Directory Domain Services (AD DS)) 建立和設定的「自我管理」網域。 然後由您繼續管理這些資源。

有了 Azure AD DS，Microsoft 就會為您部署和維護核心服務元件，如同「受控網域」體驗。 您不需要針對 VM、Windows Server OS 或網域控制站 (DC) 等元件部署、管理、修補及保護 AD DS 基礎結構。

Azure AD DS 為傳統的自我管理 AD DS 環境提供較小的功能子集，可減少部分設計和管理的複雜度。 例如，沒有任何 AD 樹系、網域、站台與複寫連結需要設計及維護。 您仍然可以[在 Azure AD DS 與內部部署環境之間建立樹系信任][create-forest-trust]。

對於在雲端中執行並需要存取傳統驗證機制 (例如 Kerberos 或 NTLM) 的應用程式和服務，Azure AD DS 會以最少的系統管理負荷來提供受控網域體驗。 如需詳細資訊，請參閱 [Azure AD DS 中使用者帳戶、密碼與系統管理的管理概念][administration-concepts]。

當您部署和執行自我管理 AD DS 環境時，您必須維護所有相關聯的基礎結構和目錄元件。 自我管理 AD DS 環境會有額外的維護負荷，但是您之後可以執行其他工作，例如延伸架構或建立樹系信任。

在提供身分識別給雲端中應用程式和服務的自我管理 AD DS 環境中，一般部署模型包括下列各項：

* **獨立的僅限雲端 AD DS** - Azure VM 會設定為網域控制站，並建立獨立的僅限雲端 AD DS 環境。 此 AD DS 環境不會與內部部署 AD DS 環境整合。 您會使用一組不同的認證來登入及管理雲端中的 VM。
* **資源樹系部署** - Azure VM 會設定為網域控制站，而 AD DS 網域會建立為現有樹系的一部分。 然後對內部部署 AD DS 環境設定信任關係。 其他 Azure VM 可透過加入網域的方式加入雲端中的這個資源樹系。 使用者驗證會透過內部部署 AD DS 環境的 VPN/ExpressRoute 連線來執行。
* **將內部部署網域擴充至 Azure** - 使用 VPN/ExpressRoute 連線將 Azure 虛擬網路連線到內部部署網路。 Azure VM 會連線到此 Azure 虛擬網路，以加入內部部署 AD DS 環境的網域。
    * 另一個替代方式是建立 Azure VM，並將其升級為來自內部部署 AD DS 網域的複本網域控制站。 這些網域控制站會透過內部部署 AD DS 環境的 VPN/ExpressRoute 連線來複寫。 內部部署 AD DS 網域會有效擴充到 Azure。

下表概述您組織可能需要的一些功能，以及受控 Azure AD DS 網域或自我管理 AD DS 網域之間的差異：

| **功能** | **Azure AD DS** | **自我管理 AD DS** |
| ----------- |:---------------:|:----------------------:|
| **受控服務**                               | **&#x2713;** | **&#x2715;** |
| **安全的部署**                            | **&#x2713;** | 系統管理員保護部署的安全 |
| **DNS 伺服器**                                    | **&#x2713;** (受控服務) |**&#x2713;** |
| **網域或企業系統管理員權限** | **&#x2715;** | **&#x2713;** |
| **加入網域**                                   | **&#x2713;** | **&#x2713;** |
| **使用 NTLM 和 Kerberos 的網域驗證** | **&#x2713;** | **&#x2713;** |
| **Kerberos 限制委派**               | 資源型 | 資源型與帳戶型|
| **自訂 OU 結構**                           | **&#x2713;** | **&#x2713;** |
| **群組原則**                                  | **&#x2713;** | **&#x2713;** |
| **結構描述延伸模組**                             | **&#x2715;** | **&#x2713;** |
| **AD 網域/樹系信任**                     | **&#x2713;** (僅限單向輸出樹系信任) | **&#x2713;** |
| **安全 LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP 讀取**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP 寫入**                                    | **&#x2713;** (在受控網域內) | **&#x2713;** |
| **地理位置分散部署**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS 與 Azure AD

Azure AD 可讓您管理組織所用裝置的身分識別，並控制這些裝置對公司資源的存取。 使用者也可以向 Azure AD 註冊他們個人的裝置 (自備 (BYO) 模型)，以取得裝置的身分識別。 接著，當使用者登入 Azure AD 並使用裝置來存取受保護的資源時，Azure AD 就會驗證裝置。 您可以使用 Microsoft Intune 等行動裝置管理 (MDM) 軟體來管理裝置。 此管理功能可讓您限制受控和符合原則的裝置對敏感資源的存取權。

傳統電腦和膝上型電腦也可以加入 Azure AD。 這項機制提供的優勢如同向 Azure AD 註冊個人裝置，例如允許使用者使用其公司認證來登入裝置。

Azure AD 聯結裝置提供下列優點：

* 單一登入 (SSO) 至 Azure AD 所保護的應用程式。
* 在跨裝置之間進行使用者設定的企業符合原則漫遊。
* 使用您公司的認證存取商務 Windows 市集。
* Windows Hello 企業版。
* 對與公司原則相容的裝置限制應用程式和資源存取權。

不論裝置是否有包含內部部署 AD DS 環境的混合式部署，都可以加入 Azure AD。 下表概述常見的裝置擁有權模型，以及其加入網域的典型方式：

| **裝置類型**                                        | **裝置平台**             | **機制**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| 個人裝置                                          | Windows 10、iOS、Android、macOS | 已註冊的 Azure AD    |
| 公司所擁有但未加入內部部署 AD DS 的裝置 | Windows 10                       | 已聯結的 Azure AD        |
| 公司所擁有且已加入內部部署 AD DS 的裝置  | Windows 10                       | 已聯結的混合式 Azure AD |

在已加入 Azure AD 或已註冊的裝置上，使用者驗證會使用以最新 OAuth/OpenID Connect 為基礎的通訊協定。 這些通訊協定皆設計為可透過網際網路運作，且非常適合行動裝置使用，讓使用者可以從任何位置存取公司資源。

裝置加入 Azure AD DS 後，應用程式就可以使用 Kerberos 和 NTLM 通訊協定進行驗證，因此可支援遷移至 Azure VM 的繼承應用程式，作為隨即轉移策略的一部分。 下表概述如何呈現裝置及針對目錄進行自我驗證的差異：

| **層面**                      | **已加入 Azure AD**                                 | **已加入 Azure AD DS**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| 控制裝置的機制            | Azure AD                                            | Azure AD DS 受控網域                                                |
| 在目錄中的表示法 | Azure AD 目錄中的裝置物件            | Azure AD DS 受控網域中的電腦物件                        |
| 驗證                  | 以 OAuth / OpenID Connect 為基礎的通訊協定              | Kerberos 和 NTLM 通訊協定                                               |
| 管理性                      | Intune 等行動裝置管理 (MDM) 軟體 | 群組原則                                                              |
| 網路功能                      | 透過網際網路運作                             | 必須與受控網域部署所在的虛擬網路連線或對等互連 |
| 適用於...                    | 使用者行動裝置或桌面裝置                  | 部署在 Azure 中的伺服器 VM                                              |

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure AD DS，請[使用 Azure 入口網站建立 Azure AD DS 受控網域][tutorial-create]。

您也可以深入了解 [Azure AD DS 中使用者帳戶、密碼與系統管理的管理概念][administration-concepts]與[受控網域中的物件和認證如何同步][synchronization]。

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview
[create-forest-trust]: tutorial-create-forest-trust.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
