---
title: Azure 公用雲端中的隔離 | Microsoft Docs
description: 瞭解 Azure 如何為惡意和非惡意使用者提供隔離，並為架構設計人員提供各種隔離選項。
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: 42582c9474647c4c203bd0cafae0be664398ba41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533898"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Azure 公用雲端中的隔離

Azure 可讓您在共用的實體基礎結構上執行 (Vm) 的應用程式和虛擬機器。 在雲端環境中執行應用程式的主要經濟動機之一是，能夠將共用資源的成本分散到多位客戶。 這種多重租用的作法會以低成本在不同客戶間進行資源的多工處理來提升效率。 不幸的是，它也會導致下列風險：共用實體伺服器和其他基礎結構資源來執行您的機密應用程式和 VM，而它們或許隸屬於可能惡意的任意使用者。

本文概述 Azure 如何針對惡意和非惡意使用者提供隔離，並可為架構設計人員提供各種隔離選項，以作為架構雲端解決方案的指南。

## <a name="tenant-level-isolation"></a>租用戶層級隔離

雲端運算主要優點之一是同時在許多客戶間共用之通用基礎結構的概念，從而導致規模經濟。 這個概念稱為多重租用。 Microsoft 會持續努力，以確保 Microsoft Cloud Azure 的多租用戶架構支援安全性、機密性、隱私權、完整性和可用性標準。

在啟用雲端的工作地點，「租用戶」可以定義為擁有和管理該雲端服務之特定執行個體的用戶端或組織。 運用 Microsoft Azure 所提供的身分識別平台，租用戶只是貴組織在註冊 Microsoft 雲端服務時所收到和擁有的專屬 Azure Active Directory (Azure AD) 執行個體。

每個 Azure AD 目錄都不同，並與其他 Azure AD 目錄分開。 就像公司辦公大樓是您組織特有的安全資產，Azure AD 目錄也是設計成僅供您組織使用的安全資產。 Azure AD 架構會隔離客戶資料與身分識別資訊，避免兩者混淆。 這表示某個 Azure AD 目錄的使用者和系統管理員無法意外或惡意存取另一個目錄中的資料。

### <a name="azure-tenancy"></a>Azure 租用

Azure 租用 (Azure 訂用帳戶) 是指「客戶/計費」關聯性，以及 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) 中唯一的[租用戶](../../active-directory/develop/quickstart-create-new-tenant.md)。 Microsoft Azure 中的租用戶層級隔離是使用 Azure Active Directory 及其所提供的[角色型控制](../../role-based-access-control/overview.md)來達成。 每個 Azure 訂用帳戶都會與一個 Azure Active Directory (AD) 目錄相關聯。

該目錄中的使用者、群組和應用程式可以管理 Azure 訂用帳戶中的資源。 您可以使用 Azure 入口網站、Azure 命令列工具或 Azure 管理 API 來指派這些存取權限。 Azure AD 租用戶邏輯上是使用安全性界限來隔離，如此一來就沒有任何客戶可以存取或危害共同的租用戶 (不論是惡意或意外)。 Azure AD 是在已隔離網路區段上隔離之「裸機」伺服器上執行的，其中主機層級的封包篩選和 Windows 防火牆會封鎖來路不明的連接和流量。

- 要存取 Azure AD 中的資料，必須透過 Security Token Service (STS) 進行使用者驗證。 授權系統會使用使用者的存在情形、啟用狀態和角色的相關資訊，判斷這名使用者在此工作階段中對目標租用戶的存取要求是否已獲授權。

![Azure 租用](./media/isolation-choices/azure-isolation-fig1.png)

- 租用戶是獨立的容器，而且彼此間沒有任何關聯性。

- 除非租用戶管理員透過同盟或佈建來自其他租用戶的使用者帳戶授與存取權，否則租用戶之間無法存取。

- 實際存取組成 Azure AD 服務的伺服器，以及直接存取 Azure AD 的後端系統，會受到限制。

- Azure AD 使用者無法存取實體資產或位置，因此，不可能略過以下所述的邏輯 RBAC 原則檢查。

針對診斷與維護需求，必須使用採用 Just-In-Time 權限提高系統的作業模型。 Azure AD Privileged Identity Management (PIM) 引進合格系統管理員的概念。合格的系統 [管理員](../../active-directory/privileged-identity-management/pim-configure.md) 應該是現在需要特殊許可權存取權的使用者，而不是每天都有。 在使用者需要存取權之前，角色會處於非作用中狀態，然後使用者須完成啟用程序，才能在一段預定的時間內成為作用中的系統管理員。

![Azure AD 特殊權限身分識別管理](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory 會透過租用戶單獨擁有且管理之容器內的原則和權限，在它自己受保護的容器中裝載每個租用戶。

租用戶容器的概念已完全深入目錄服務的所有層級，涵蓋範圍可從入口網站一直到永續性儲存體。

即使將來自多個 Azure Active Directory 租用戶的中繼資料儲存於同一個實體磁碟，在目錄服務所定義之容器以外的容器間還是不會有任何關聯性，而目錄服務是由租用戶管理員所決定。

### <a name="azure-role-based-access-control-azure-rbac"></a>Azure 角色型存取控制 (Azure RBAC)

Azure [ (AZURE RBAC) 的 azure 角色型存取控制](../../role-based-access-control/overview.md)可協助您在 azure 訂用帳戶中共用各種可在 azure 訂用帳戶中使用的元件，方法是提供更細緻的 azure 存取管理。 Azure RBAC 可讓您隔離組織內的責任，並根據使用者需要哪些權限執行其工作來授與他們存取權。 您不需為每個人授與 Azure 訂用帳戶或資源中無限制的權限，而是只允許執行特定的動作。

Azure RBAC 有適用於所有資源類型的三個基本角色：

- **擁有** 者具有所有資源的完整存取權，包括將存取權委派給其他人的許可權。

- **參與者** 可以建立和管理所有類型的 Azure 資源，但無法將存取權授與其他人。

- **讀者** 可以看到現有的 Azure 資源。

![Azure 角色型存取控制 (Azure RBAC)](./media/isolation-choices/azure-isolation-fig3.png)

Azure 中的其餘 Azure 角色允許管理特定的 Azure 資源。 例如，「虛擬機器參與者」角色可讓使用者建立和管理虛擬機器。 但不會授予他們存取虛擬機器所連接之 Azure 虛擬網路或子網路的存取權。

[RBAC 內建角色](../../role-based-access-control/built-in-roles.md)會列出 Azure 中可用的角色。 它會指定每個內建角色授與使用者的作業和範圍。 如果您想要定義自己的角色，獲得更進一步控制，請參閱如何建立 [Azure RBAC 中的自訂角色](../../role-based-access-control/custom-roles.md)。

Azure Active Directory 的一些其他功能包括：

- Azure AD 會啟用 SaaS 應用程式的 SSO，而無論應用程式裝載於何處。 有些應用程式會與 Azure AD 同盟，有些則使用密碼 SSO。 同盟應用程式也可以支援使用者布建和 [密碼](https://www.techopedia.com/definition/31415/password-vault)保存庫。

- 對 [Azure 儲存體](https://azure.microsoft.com/services/storage/)的資料存取可透過驗證來控制。 每個儲存體帳戶都有主要金鑰 ([儲存體帳戶金鑰](../../storage/common/storage-create-storage-account.md)，或稱 SAK) 和次要金鑰 (共用存取簽章，或稱 SAS)。

- Azure AD 可透過與內部部署目錄的同盟、同步和複寫提供「身分識別即服務」(使用 [Active Directory Federation Services](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md))。

- [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) 是一種多因素驗證服務，需要使用者同時使用行動裝置應用程式、通話或簡訊來驗證登入。 它可與 Azure Active Directory 搭配使用，來協助保護內部部署資源和 Azure Multi-Factor Authentication Server 的安全，它還可以使用 SDK 來與自訂應用程式和目錄搭配使用。

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) 可讓您將 Azure 虛擬機器加入 Active Directory 網域，而不需部署網域控制站。 您可以登入使用公司的 Active Directory 認證登入這些虛擬機器，並使用群組原則管理加入網域的虛擬機器，以對您所有的 Azure 虛擬機器強制執行安全性基準。

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) 所提供的高可用性全域身分識別管理服務，可用於處理數億個身分識別的消費者端應用程式。 此服務可跨行動及 Web 平台進行整合。 透過可自訂的體驗，讓您的消費者可以使用其現有的社交帳戶或建立認證，來登入您所有的應用程式。

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>從 Microsoft 管理員及資料刪除中隔離

Microsoft 採取強固的措施來保護您的資料，以免受到未經授權之使用者的不當存取或使用。 這些作業程序和控制都受到 [Online Services 條款 (英文)](https://aka.ms/Online-Services-Terms) 所支援，這類條款會提供管理資料存取的契約承諾。

- Microsoft 工程師對於您在雲端中的資料預設並無存取權。 而是只有在必要時，才會在有管理監督的情況下授與他們權限。 該存取權會受到仔細的控制及記錄，並於不再需要時撤銷。
- Microsoft 可能會雇用其他公司代表 Microsoft 提供有限的服務。 轉包商只有在遞送我們雇用他們來提供的服務時才能存取客戶資料，並禁止基於任何其他目的使用該資料。 此外，他們會受到合約所束縛，以保持我們客戶資訊的機密性。

Microsoft 與公認的稽核公司只會基於合法商業用途，定期驗證具有已經過審核之認證的商業服務 (例如 ISO/IEC 27001)，它們會執行範例稽核來證明該存取權。 您一律可基於任何原因，隨時存取您自己的客戶資料。

如果您刪除任何資料，Microsoft Azure 即會刪除該資料，包括任何快取或備份的複本。 針對範圍內的服務，將在保留期間結束後 90 天內執行該項刪除 (範圍內的服務定義於 [Online Services 條款 (英文)](https://aka.ms/Online-Services-Terms) 的＜資料處理條款＞一節中)。

如果用於儲存的磁碟機發生硬體故障，在 Microsoft 將它送回給製造商進行替換或修復之前，會先安全地[清除或終結 (英文)](https://microsoft.com/trustcenter/privacy/you-own-your-data) 它。 磁碟機上的資料會加以覆寫，以確保無法透過任何方式復原資料。

## <a name="compute-isolation"></a>計算隔離

Microsoft Azure 提供各種雲端式計算服務，其中包含各式各樣的計算執行個體和服務，可自動相應增加或縮小以符合您應用程式或企業的需求。 這些計算執行個體與服務會在多個層級上提供隔離，以保護資料而不會犧牲客戶所要求之組態中的彈性。

### <a name="isolated-virtual-machine-sizes"></a>隔離虛擬機器大小

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>專用主機

除了上一節所述的隔離主機之外，Azure 也提供專用主機。 Azure 中的專用主機提供可裝載一或多部虛擬機器的實體伺服器，而且專用於單一 Azure 訂用帳戶的服務。 專用主機提供實體伺服器層級的硬體隔離。 系統不會在您的主機上放置其他 Vm。 專用主機會部署在相同的資料中心，並與其他非隔離的主機共用相同的網路和基礎的儲存基礎結構。 如需詳細資訊，請參閱 [Azure 專用主機](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)的詳細總覽。

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>根 VM 與客體 VM 之間的 Hyper-V 和根 OS 隔離

Azure 的計算平台會以機器虛擬化為基礎，這表示所有客戶程式碼都會在 Hyper-V 虛擬機器中執行。 在每個 Azure 節點 (或網路端點) 上，都有一個 Hypervisor 會直接在硬體上執行，並將節點分成不同數量的客體虛擬機器 (VM)。

![根 VM 與客體 VM 之間的 Hyper-V 和根 OS 隔離](./media/isolation-choices/azure-isolation-fig4.jpg)

每個節點也會有一部特殊的根 VM，其會執行主機 OS。 關鍵界限是隔離客體 VM 與根 VM，以及客體 VM 的彼此隔離 (由 Hypervisor 和根 OS 所管理)。 Hypervisor/根 OS 配對運用 Microsoft 數十年的作業系統安全性體驗，以及最近從 Microsoft Hyper-V 中學到的資訊，來提供客體 VM 的嚴密隔離。

Azure 平台使用虛擬化環境。 使用者執行個體以未存取實體主機伺服器的獨立虛擬機器形式運作。

Azure Hypervisor 作用相當於微核心，可將來自客體虛擬機器的所有硬體存取要求傳遞至主機，以使用名為 VMBus 的共用記憶體介面進行處理。 這可以防止使用者取得系統的原始讀取/寫入/執行存取權，並降低共用系統資源的風險。

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>進階 VM 放置演算法與提供保護來抵禦旁道攻擊

任何跨 VM 的攻擊都牽涉到兩個步驟︰將受到敵人控制的 VM 放置於和其中一部受害 VM 相同的主機上，然後破壞隔離界限以竊取機密的受害資訊，或者基於貪婪或破壞行為來影響它的效能。 Microsoft Azure 會透過兩個步驟提供保護：使用進階的 VM 放置演算法，並提供保護來抵禦所有已知的旁道攻擊，包括鄰點干擾的 VM。

### <a name="the-azure-fabric-controller"></a>Azure 網狀架構控制器

Azure 網狀架構控制器會負責配置基礎結構資源給租用戶工作負載，以及管理從主機到虛擬機器的單向通訊。 Azure 網狀架構控制器的 VM 放置演算法非常複雜，而且幾乎不可能以實體主機層級進行預測。

![Azure 網狀架構控制器](./media/isolation-choices/azure-isolation-fig5.png)

Azure hypervisor 會強制執行虛擬機器之間的記憶體和程序區隔，並安全地將網路流量路由傳送至客體 OS 租用戶。 這會消除 VM 層級發生旁道攻擊的可能性。

在 Azure 中，根 VM 是特殊的：它會執行稱為根 OS 的強化作業系統，其中裝載了網狀架構代理程式 (FA)。 接著會使用 FAs 來管理來賓代理程式， (客戶 Vm 上的客體作業系統內的正式) 。 FA 也會管理儲存體節點。

Azure Hypervisor、根 OS/FA 和客戶 VM/GA 的集合會組成一個計算節點。 FA 是由網狀架構控制器 (FC) 管理的，其存在於計算和儲存體節點 (計算和儲存體叢集是由個別 FC 管理的) 以外的地方。 如果客戶於應用程式正在執行時更新其組態檔，FC 就會與 FA 通訊，然後連絡 GA，後者會通知應用程式組態變更。 發生硬體故障時，FC 將自動尋找可用的硬體，並在該處重新啟動 VM。

![Azure 網狀架構控制器](./media/isolation-choices/azure-isolation-fig6.jpg)

從網狀架構控制器到代理程式的通訊是單向的。 代理程式會實作受 SSL 保護的服務，此服務只會回應來自控制站的要求。 它無法起始與控制器或其他特殊權限之內部節點的連接。 FC 會處理所有回應，如同它們全都不受信任。

![網狀架構控制器](./media/isolation-choices/azure-isolation-fig7.png)

隔離會從客體 VM 的根 VM 延伸，以及從客體 VM 彼此間延伸。 計算節點也會為了加強保護而從儲存體節點隔離出來。

Hypervisor 與主機 OS 提供網路封包 - 篩選器，以協助保證不受信任的虛擬機器無法產生偽裝的流量，或是接收無法定址到它們的流量、將流量導向受保護的基礎架構端點，或傳送/接收不當的廣播流量。

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>網狀架構控制器代理程式設定來隔離 VM 的其他規則

根據預設，在建立虛擬機器時會封鎖所有流量，接著網狀架構控制器代理程式會設定封包篩選器，以新增規則和例外狀況來允許授權的流量。

以下為要進行程式設計的兩種規則：

- **電腦設定或基礎結構規則：** 預設會封鎖所有通訊。 有部分例外狀況可允許虛擬機器傳送與接收 DHCP 和 DNS 流量。 虛擬機器也可以將流量傳送至「公用」網際網路，以及將流量傳送至同一個 Azure 虛擬網路和 OS 啟用伺服器內的其他虛擬機器。 虛擬機器允許的連出目的地清單不包含 Azure 路由器子網路、Azure 管理和其他 Microsoft 屬性。
- **角色設定檔案：** 這會根據租使用者的服務模型，定義 (Acl) 的輸入存取控制清單。

### <a name="vlan-isolation"></a>VLAN 隔離

每個叢集中有三個 VLAN：

![VLAN 隔離](./media/isolation-choices/azure-isolation-fig8.jpg)

- 主要 VLAN：互連未受信任的客戶節點
- FC VLAN：包含受信任的 FC 和支援系統
- 裝置 VLAN：包含受信任的網路和其他基礎結構裝置

允許從 FC VLAN 到主要 VLAN 的通訊，但無法起始從主要 VLAN 到 FC VLAN 的通訊。 從主要 VLAN 到裝置 VLAN 的通訊也會遭到封鎖。 這樣可確保即使執行客戶程式碼的節點遭到危害，它也不能攻擊 FC 或裝置 VLAN 上的節點。

## <a name="storage-isolation"></a>儲存體隔離

### <a name="logical-isolation-between-compute-and-storage"></a>計算和儲存體之間的邏輯隔離

Microsoft Azure 的基本設計是將以 VM 為基礎的計算與儲存體分隔開來。 這種區隔讓計算和儲存體能夠各自調整，更容易提供多重租用和隔離。

因此，除了透過邏輯方式，Azure 儲存體會在沒有連到 Azure 計算之網路連線的個別硬體上執行。 這表示，在建立虛擬磁碟時，不會將磁碟空間配置給它的整個產能。 而會改為建立表格，將虛擬磁碟上的位址對應至實體磁碟上的區域，而該表格最初是空的。 **客戶第一次將資料寫入虛擬磁碟、配置實體磁碟上的空間，然後在表格中放置它的指標。**

### <a name="isolation-using-storage-access-control"></a>使用儲存體存取控制進行隔離

**Azure 儲存體中的存取控制**具有簡單的存取控制模型。 每個 Azure 訂用帳戶都能建立一或多個儲存體帳戶。 每個儲存體帳戶都會有單一祕密金鑰，可用來控制存取該儲存體帳戶中的所有資料。

![使用儲存體存取控制進行隔離](./media/isolation-choices/azure-isolation-fig9.png)

** Azure 儲存體資料 (包括表格)** 可透過 [SAS (共用存取簽章)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 權杖來控制，該權杖會授與限定範圍的存取權。 SAS 會透過查詢範本 (URL) 來建立，此 URL 是利用 [SAK (儲存體帳戶金鑰)](https://msdn.microsoft.com/library/azure/ee460785.aspx) 簽署的。 該[簽署的 URL](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 可以提供給另一個程序 (也就是委派)，然後填入查詢的詳細資料，並提出儲存體服務的要求。 SAS 可讓您對用戶端授與限時的存取權，而不需揭露儲存體帳戶的祕密金鑰。

SAS 意謂著我們可以將儲存體帳戶中物件的有限權限授與用戶端，讓該用戶端可以在一段指定的時間內使用一組指定的權限進行存取。 我們可以在不須分享您帳戶存取金鑰的情況下，授與這些有限的權限。

### <a name="ip-level-storage-isolation"></a>IP 層級儲存體隔離

您可以建立防火牆，並且為受信任的用戶端定義 IP 位址範圍。 使用 IP 位址範圍時，只有具有定義範圍內 IP 位址的用戶端可以連接到 [Azure 儲存體](../../storage/blobs/security-recommendations.md)。

IP 儲存體資料可透過網路機制受到保護，以避免未經授權之使用者的存取，該機制可用來將流量的專用通道配置到 IP 儲存體。

### <a name="encryption"></a>加密

Azure 提供下列加密類型來保護資料：

- 傳輸中加密
- 待用加密

#### <a name="encryption-in-transit"></a>傳輸中加密

傳輸中加密是透過網路傳輸資料時用來保護資料的機制。 透過 Azure 儲存體，您可以使用下列各項來保護資料：

- [傳輸層級加密](../../storage/blobs/security-recommendations.md)，例如當您將資料傳入或傳出 Azure 儲存體時的 HTTPS。
- [網路加密](../../storage/blobs/security-recommendations.md)，例如 Azure 檔案共用的 SMB 3.0 加密。
- [用戶端加密](../../storage/blobs/security-recommendations.md)，可在資料傳輸至儲存體之前加密資料，並在資料從儲存體傳出後解密。

#### <a name="encryption-at-rest"></a>待用加密

對於許多組織而言，待用 [資料加密](isolation-choices.md) 是資料隱私權、合規性及資料主權的必要步驟。 有三個 Azure 功能可提供「待用」資料的加密。

- [儲存體服務加密](../../storage/blobs/security-recommendations.md) 可讓您要求儲存體服務在將資料寫入 Azure 儲存體時自動加密資料。
- [用戶端加密](../../storage/blobs/security-recommendations.md) 也提供待用加密的功能。
- [Azure 磁碟加密](../azure-security-disk-encryption-overview.md) 允許您加密 IaaS 虛擬機器所使用的作業系統磁碟和資料磁碟。

#### <a name="azure-disk-encryption"></a>Azure 磁碟加密

適用於虛擬機器 (VM) 的 [Azure 磁碟加密](../azure-security-disk-encryption-overview.md)會使用您在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 中所控制的金鑰與原則來將 VM 磁碟 (包括開機和資料磁碟) 加密，以協助您達成組織安全性與合規性需求。

Windows 的磁碟加密解決方案是建基於 [Microsoft BitLocker 磁碟機加密](https://technet.microsoft.com/library/cc732774.aspx)，而 Linux 解決方案是建基於 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)。

在 Microsoft Azure 中啟用時，解決方案會對 IaaS VM 支援下列案例：

- 與 Azure 金鑰保存庫整合
- 標準層 VM：A、D、DS、G、GS 等系列 IaaS VM
- 在 Windows 和 Linux IaaS VM 上啟用加密
- 為 Windows IaaS VM 的 OS 和資料磁碟機停用加密
- 為 Linux IaaS VM 的資料磁碟機停用加密
- 在執行 Windows 用戶端 OS 的 IaaS VM 上啟用加密
- 在具有掛接路徑的磁碟區上啟用加密
- 在使用 [mdadm (英文)](https://en.wikipedia.org/wiki/Mdadm) 設定等量磁碟 (RAID) 的 Linux VM 上啟用加密
- 在使用資料磁碟適用之 [LVM (邏輯磁碟區管理員) (英文)](https://msdn.microsoft.com/library/windows/desktop/bb540532) 的 Linux VM 上啟用加密
- 在使用儲存空間設定的 Windows VM 上啟用加密
- 所有 Azure 公用區域皆受到支援

解決方案不支援此版本中的下列案例、功能和技術：

- 基本層 IaaS VM
- 在 Linux IaaS VM 的 OS 磁碟機上停用加密
- 使用傳統 VM 建立方法所建立的 IaaS VM
- 與您的內部部署金鑰管理服務整合
- Azure 檔案 (共用檔案系統)、網路檔案系統 (NFS)、動態磁碟區和以軟體型 RAID 系統所設定的 Windows VM

## <a name="sql-database-isolation"></a>SQL Database 隔離

SQL Database 是 Microsoft Cloud 中以領先市場的 Microsoft SQL Server 引擎為基礎的關聯式資料庫服務，能夠處理各種任務關鍵性工作負載。 SQL Database 提供帳戶層級的可預測資料隔離 (以地理位置/區域為基礎和以網路為基礎)；幾乎全都不需管理。

### <a name="sql-database-application-model"></a>SQL Database 應用程式模型

[Microsoft SQL Database](../../azure-sql/database/single-database-create-quickstart.md) 是以 SQL Server 技術為基礎的雲端式關係資料庫服務。 它會在雲端中提供由 Microsoft 裝載的高可用性、可調整、多租用戶的資料庫服務。

從應用程式的觀點來看，SQL Database 提供下列階層：每個層級都有一對多層級的內含專案。

![SQL Database 應用程式模型](./media/isolation-choices/azure-isolation-fig10.png)

帳戶和訂用帳戶是關聯計費與管理的 Microsoft Azure 平台概念。

邏輯 SQL 伺服器和資料庫是 SQL Database 特有的概念，而且是使用 SQL Database （提供 OData 和 TSQL 介面或透過 Azure 入口網站）進行管理。

SQL Database 中的伺服器不是實體或 VM 實例，而是儲存在所謂的「邏輯 master」資料庫中的資料庫集合，也就是共用管理與安全性原則。

![SQL Database](./media/isolation-choices/azure-isolation-fig11.png)

邏輯 master 資料庫包括：

- 用來連接到伺服器的 SQL 登入
- 防火牆規則

來自相同伺服器之資料庫的計費和使用量相關資訊不保證會在叢集中相同的實體實例上，而是在連接時，應用程式必須提供目標資料庫名稱。

從客戶的觀點來看，伺服器是在地理圖形區中建立，而實際建立的伺服器則是在該區域的其中一個叢集中進行。

### <a name="isolation-through-network-topology"></a>透過網路拓撲進行隔離

當您建立伺服器並註冊其 DNS 名稱時，DNS 名稱會指向放置伺服器的特定資料中心內稱為「閘道 VIP」位址。

在 VIP (虛擬 IP 位址) 背後，我們擁有一組無狀態閘道服務。 通常，若在多個資料來源 (master 資料庫、使用者資料庫等) 之間需要進行協調，就會牽涉到閘道。 閘道服務會實作下列各項：

- **TDS 連接 Proxy 處理。** 這包括在後端叢集中尋找使用者資料庫、實作登入順序，然後將 TDS 封包轉送至後端，並往回執行。
- **資料庫管理。** 這包括實作執行一組工作流程來執行 CREATE/ALTER/DROP 資料庫作業。 資料庫作業可以透過探查 TDS 封包或明確的 OData API 來叫用。
- CREATE/ALTER/DROP 登入/使用者作業
- 經由 OData API 的伺服器管理作業

![透過網路拓撲進行隔離](./media/isolation-choices/azure-isolation-fig12.png)

閘道背後的層級稱為「後端」。 這是以高可用性方式儲存所有資料的地方。 每個部分的資料都可說成屬於某個「資料分割」或「容錯移轉單元」，它們每一個至少要有三個複本。 複本會透過 SQL Server 引擎加以儲存和複寫，並由通常稱為「網狀架構」的容錯移轉系統所管理。

做為安全性防範措施，後端系統通常不會輸出通訊至其他系統。 這會保留給前端 (閘道) 層中的系統。 閘道層的機器在後端機器上的權限有限，可做為深度防禦機制來將攻擊面降至最低。

### <a name="isolation-by-machine-function-and-access"></a>透過機器功能與存取進行隔離

SQL Database (是由在不同電腦功能上執行的服務所組成。 SQL Database 分成「後端」雲端資料庫和「前端」 (閘道/管理) 環境中，而且流量的一般原則只會進入後端，而不是輸出。前端環境可以與其他服務的外界通訊，而且一般而言，在後端中只有有限的許可權， (足以呼叫) 所需的進入點。

## <a name="networking-isolation"></a>網路隔離

Azure 部署具有多層網路隔離。 下圖顯示 Azure 提供給客戶的各種網路隔離層級。 這些層級既是 Azure 平台本身的原生功能，也是客戶定義的功能。 從網際網路往內，Azure DDoS 會針對 Azure 進行的大規模攻擊提供隔離。 下一個隔離層級是客戶定義的公用 IP 位址 (端點)，用來判斷哪種流量可通過雲端服務進入虛擬網路。 原生 Azure 虛擬網路隔離可確保與其他所有網路完全隔離，而且流量只能流經使用者設定的路徑和方法。 這些路徑和方法就是下一層，可利用 NSG、UDR 和網路虛擬設備來建立隔離界限，以保護受保護網路中的應用程式部署。

![網路隔離](./media/isolation-choices/azure-isolation-fig13.png)

**流量隔離：**[虛擬網路](../../virtual-network/virtual-networks-overview.md)是 Azure 平臺上的流量隔離界限。 一個虛擬網路中的虛擬機器 (VM) 無法與不同虛擬網路中的 VM 直接通訊，即使兩個虛擬網路是由同一位客戶所建立也一樣。 隔離是很重要的屬性，可確保客戶 VM 和通訊仍然隱蔽於虛擬網路內。

[子網路](../../virtual-network/virtual-networks-overview.md)使用以 IP 範圍為基礎的虛擬網路，來提供額外的隔離層級。 虛擬網路中的 IP 位址，您可以將虛擬網路分成多個子網路以便進行組織和獲得安全性。 部署至 VNet 內 (相同或不同) 子網路的 VM 和 PaaS 角色執行個體不需要進行額外設定就可以彼此通訊。 您也可以設定[網路安全性群組 (NSG)](../../virtual-network/virtual-networks-overview.md)，根據 NSG 之存取控制清單 (ACL) 中設定的規則，來允許或拒絕移至 VM 執行個體的網路流量。 NSG 可與子網路或該子網路內的個別 VM 執行個體相關聯。 當 NSG 與子網路相關聯時，ACL 規則會套用到該子網路中的所有 VM 執行個體。

## <a name="next-steps"></a>後續步驟

- 瞭解 [Windows Azure 虛擬網路中的電腦網路隔離選項](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)。 這包括傳統前端和後端案例，特定後端網路或子網中的電腦可能只允許特定用戶端或其他電腦根據 IP 位址的允許清單連接到特定端點。

- 瞭解 [Azure 中的虛擬機器隔離](../../virtual-machines/isolation.md)。 Azure 計算提供隔離于特定硬體類型的虛擬機器大小，並專用於單一客戶。
