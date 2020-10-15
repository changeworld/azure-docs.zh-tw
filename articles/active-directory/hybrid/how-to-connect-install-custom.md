---
title: 自訂 Azure Active Directory Connect 的安裝
description: 本文說明 Azure AD Connect 的自訂安裝選項。 使用下列指示，透過 Azure AD Connect 安裝 Active Directory。
services: active-directory
keywords: 何謂 Azure AD Connect、安裝 Active Directory、Azure AD 的必要元件
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096346"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Azure Active Directory Connect 的自訂安裝
當您想要安裝更多選項時，請使用 Azure Active Directory (Azure AD) 連接的 *自訂設定* 。 使用這些設定，例如，如果您有多個樹系，或您想要設定選用功能。 在 [快速安裝](how-to-connect-install-express.md) 不符合您的部署或拓撲需求的所有情況下，使用自訂設定。

必要條件：
- [下載 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)。
- 完成 Azure AD Connect 中的先決條件步驟 [：硬體和必要條件](how-to-connect-install-prerequisites.md)。 
- 請確定您有 [Azure AD Connect 帳戶和許可權](reference-connect-accounts-permissions.md)中所描述的帳戶。

## <a name="custom-installation-settings"></a>自訂安裝設定 

若要設定 Azure AD Connect 的自訂安裝，請流覽下列各節所述的 wizard 頁面。

### <a name="express-settings"></a>快速設定
在 [ **快速設定** ] 頁面上，選取 [ **自訂** ] 以啟動自訂設定安裝。  本文的其餘部分會引導您完成自訂安裝程式。 使用下列連結可快速移至特定頁面的資訊：

- [必要元件](#install-required-components)
- [使用者登入](#user-sign-in)
- [連接至 Azure AD](#connect-to-azure-ad)
- [同步](#sync-pages)

### <a name="install-required-components"></a>安裝必要的元件
當您安裝同步處理服務時，您可以將選用設定區段保留為未選取狀態。 Azure AD Connect 會自動設定所有專案。 它會設定 SQL Server 2012 Express LocalDB 實例、建立適當的群組，並指派許可權。 如果您想要變更預設值，請清除適當的方塊。  下表摘要說明這些選項，並提供其他資訊的連結。 

![顯示 Azure AD Connect 中必要安裝元件之選擇性選取專案的螢幕擷取畫面。](./media/how-to-connect-install-custom/requiredcomponents2.png)

| 選用設定 | 描述 |
| --- | --- |
|指定自訂安裝位置| 可讓您變更 Azure AD Connect 的預設安裝路徑。|
| 使用現有的 SQL Server |可讓您指定 SQL Server 名稱和實例名稱。 如果您已經有想要使用的資料庫伺服器，請選擇此選項。 在 [ **實例**名稱] 中，輸入實例名稱、逗號和埠號碼（如果您的 SQL Server 實例未啟用流覽）。  然後，指定 Azure AD Connect 資料庫的名稱。  您的 SQL 許可權會決定是否可以建立新的資料庫，或者您的 SQL 系統管理員必須事先建立資料庫。  如果您有 SQL Server 系統管理員 (SA) 許可權，請參閱 [使用現有的資料庫安裝 Azure AD Connect](how-to-connect-install-existing-database.md)。  如果您已 (DBO) 委派許可權，請參閱 [使用 SQL 委派的系統管理員許可權來安裝 Azure AD Connect](how-to-connect-install-sql-delegation.md)。 |
| 使用現有的服務帳戶 |根據預設，Azure AD Connect 會提供同步處理服務的虛擬服務帳戶。 如果您使用 SQL Server 的遠端實例或使用需要驗證的 proxy，您可以在網域中使用 *受管理的服務帳戶* 或受密碼保護的服務帳戶。 在這些情況下，請輸入您想要使用的帳戶。 若要執行安裝，您必須是 SQL 中的 SA，才能建立服務帳戶的登入認證。 如需詳細資訊，請參閱 [Azure AD Connect 帳戶與權限](reference-connect-accounts-permissions.md#adsync-service-account)。 </br></br>藉由使用最新的組建，SQL 系統管理員現在可以在頻外布建資料庫。 然後 Azure AD Connect 系統管理員可以使用資料庫擁有者許可權進行安裝。  如需詳細資訊，請參閱[使用 SQL 委派的管理員權限安裝 Azure AD Connect](how-to-connect-install-sql-delegation.md)。|
| 指定自訂同步群組 |根據預設，安裝同步處理服務時，Azure AD Connect 會在伺服器本機建立四個群組。 這些群組包括系統管理員、操作員、流覽和密碼重設。 您可以在此指定自己的群組。 群組在伺服器上必須是本機的。 它們不能位於網域中。 |
| (預覽) 匯入同步處理設定|可讓您從其他版本的 Azure AD Connect 匯入設定。  如需詳細資訊，請參閱匯 [入和匯出 Azure AD Connect 設定](how-to-connect-import-export-config.md)。|

### <a name="user-sign-in"></a>使用者登入
安裝必要元件之後，請選取使用者的單一登入方法。 下表簡要說明可用的選項。 如需登入方法的完整說明，請參閱[使用者登入](plan-connect-user-signin.md)。

![顯示 [使用者登入] 頁面的螢幕擷取畫面。 已選取 [密碼雜湊同步處理] 選項。](./media/how-to-connect-install-custom/usersignin4.png)

| 單一登入選項 | 描述 |
| --- | --- |
| 密碼雜湊同步處理 |使用者可以使用他們在內部部署網路中使用的相同密碼來登入 Microsoft 雲端服務（例如 Microsoft 365）。 使用者密碼會以密碼雜湊同步處理至 Azure AD。 在雲端中進行驗證。 如需詳細資訊，請參閱 [密碼雜湊同步](how-to-connect-password-hash-synchronization.md)處理。 |
|傳遞驗證|使用者可以使用他們在內部部署網路中使用的相同密碼來登入 Microsoft 雲端服務（例如 Microsoft 365）。  使用者密碼會透過傳遞給內部部署 Active Directory 網域控制站來進行驗證。
| 與 AD FS 同盟 |使用者可以使用他們在內部部署網路中使用的相同密碼來登入 Microsoft 雲端服務（例如 Microsoft 365）。  系統會將使用者重新導向至其內部部署 Azure 目錄同盟服務， (AD FS) 實例登入。 驗證是在內部部署進行。 |
| 與 PingFederate 同盟|使用者可以使用他們在內部部署網路中使用的相同密碼來登入 Microsoft 雲端服務（例如 Microsoft 365）。  系統會將使用者重新導向至其內部部署 PingFederate 實例以進行登入。 驗證是在內部部署進行。 |
| 請勿設定 |未安裝或設定使用者登入功能。 如果您已經有協力廠商同盟伺服器或其他解決方案，請選擇此選項。 |
|啟用單一登入|此選項同時適用于密碼雜湊同步處理和傳遞驗證。 它可為公司網路上的桌面使用者提供單一登入體驗。 如需詳細資訊，請參閱 [單一登入](how-to-connect-sso.md)。 </br></br>**注意：** 若為 AD FS 客戶，則無法使用此選項。 AD FS 已提供相同層級的單一登入。</br>

### <a name="connect-to-azure-ad"></a>連接至 Azure AD
在 [ **連接到 Azure AD]** 頁面上，輸入全域管理員帳戶和密碼。 如果您在前一頁選取了 [ **與 AD FS 同盟** ]，請不要使用您打算為同盟啟用之網域中的帳戶登入。 

您可能會想要使用預設 *onmicrosoft.com* 網域中的帳戶，該帳戶隨附于 Azure AD 租使用者。 此帳戶僅用於在 Azure AD 中建立服務帳戶。 安裝完成之後，就不會使用它。
  
![顯示 [連線至 Azure AD] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/connectaad.png)

如果您的全域管理員帳戶已啟用多重要素驗證，您可以在登入視窗中再次提供密碼，您必須完成多重要素驗證挑戰。 挑戰可能是驗證碼或來電。  

![顯示 [連線至 Azure AD] 頁面的螢幕擷取畫面。 多重要素驗證欄位會提示使用者輸入程式碼。](./media/how-to-connect-install-custom/connectaadmfa.png)

全域管理員帳戶也可以啟用 [許可權身分識別管理](../privileged-identity-management/pim-getting-started.md) 。

如果您看到錯誤或有連線問題，請參閱疑難排解連線 [問題](tshoot-connect-connectivity.md)。

## <a name="sync-pages"></a>同步頁面

下列各節說明 [ **同步** 處理] 區段中的頁面。

### <a name="connect-your-directories"></a>連接您的目錄
若要連線至 Active Directory Domain Services (Azure AD DS) ，Azure AD Connect 需要具有足夠許可權之帳戶的樹系名稱和認證。

![顯示 [連線您的目錄] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/connectdir01.png)

當您輸入樹系名稱並選取 [  **新增目錄**] 之後，就會出現一個視窗。 下表說明您的選項。

| 選項 | 描述 |
| --- | --- |
| 建立新帳戶 | 在目錄同步處理期間，建立 Azure AD Connect 需要連接到 Active Directory 樹系的 Azure AD DS 帳戶。 選取此選項之後，請輸入企業系統管理員帳戶的使用者名稱和密碼。  Azure AD Connect 使用提供的企業系統管理員帳戶來建立必要的 Azure AD DS 帳戶。 您可以採用 NetBIOS 格式或 FQDN 格式輸入網域部分。 也就是說，輸入 *FABRIKAM\administrator* 或 *FABRIKAM. com\administrator*。 |
| 使用現有帳戶 | 提供現有的 Azure AD DS 帳戶，Azure AD Connect 可在目錄同步處理期間，用來連接到 Active Directory 樹系。 您可以採用 NetBIOS 格式或 FQDN 格式輸入網域部分。 也就是說，輸入 *FABRIKAM\syncuser* 或 *FABRIKAM. com\syncuser*。 此帳戶可以是一般使用者帳戶，因為它只需要預設的讀取權限。 但視您的案例而定，您可能需要更多的許可權。 如需詳細資訊，請參閱 [Azure AD Connect 帳戶與權限](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account)。 |

![顯示 [連接目錄] 頁面和 [D 樹系帳戶] 視窗的螢幕擷取畫面，您可以在其中選擇建立新帳戶或使用現有的帳戶。](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> 從組建1.4.18.0，您無法使用企業系統管理員或網域系統管理員帳戶做為 Azure AD DS 連接器帳戶。 當您選取 [ **使用現有的帳戶**] 時，如果您嘗試輸入企業系統管理員帳戶或網域系統管理員帳戶，則會看到下列錯誤：「不允許針對您的 AD 樹系帳戶使用企業或網域系統管理員帳戶。 讓 Azure AD Connect 為您建立帳戶，或使用正確的許可權指定同步處理帳戶。」
>

### <a name="azure-ad-sign-in-configuration"></a>Azure AD 登入組態
在 [ **Azure AD 登入** 設定] 頁面上，檢查內部部署 Azure AD DS 中 (UPN) 網域的使用者主體名稱。 這些 UPN 網域已在 Azure AD 中進行驗證。 在此頁面上，您可以設定要用於 userPrincipalName 的屬性。

![螢幕擷取畫面，顯示 [Azure A D 登入設定] 頁面上的未驗證網域。](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

檢查標示為 [ **未新增** ] 或 [ **未驗證**] 的每個網域。 確定您使用的網域已在 Azure AD 中進行驗證。 確認網域之後，請選取 [迴圈重新整理] 圖示。 如需詳細資訊，請參閱 [新增和驗證網域](../fundamentals/add-custom-domain.md)。

當使用者登入 Azure AD 並 Microsoft 365 時，會使用 *userPrincipalName* 屬性。 Azure AD 應該在同步處理使用者之前，先確認網域（也稱為 UPN 尾碼）。 Microsoft 建議您保留預設屬性 userPrincipalName。 

如果 userPrincipalName 屬性是路由傳送且無法驗證，則您可以選取另一個屬性。 例如，您可以選取 [電子郵件] 作為保存登入識別碼的屬性。 當您使用 userPrincipalName 以外的屬性時，即稱為 *替代識別碼*。 

替代識別碼屬性值必須遵循 RFC 822 標準。 您可以使用替代識別碼搭配密碼雜湊同步處理、傳遞驗證和同盟。 在 Active Directory 中，屬性不能定義為多重值，即使它只有單一值也一樣。 如需替代識別碼的詳細資訊，請參閱 [傳遞驗證：常見問題](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname)。

>[!NOTE]
> 當您啟用傳遞驗證時，您必須至少有一個已驗證的網域，才能繼續進行自訂安裝程式。

> [!WARNING]
> 替代識別碼與所有 Microsoft 365 工作負載不相容。 如需詳細資訊，請參閱設定 [替代登入識別碼](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)。
>

### <a name="domain-and-ou-filtering"></a>網域和 OU 篩選
依預設， (Ou) 的所有網域和組織單位都會進行同步處理。 如果您不想將某些網域或 Ou 同步處理 Azure AD，可以清除適當的選項。  

![顯示網域和 O U 篩選頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/domainoufiltering.png)  

此頁面會設定網域型和以 OU 為基礎的篩選。 如果您打算進行變更，請參閱 [網域型篩選](how-to-connect-sync-configure-filtering.md#domain-based-filtering) 和 [OU 型篩選](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)。 某些 Ou 對於功能而言是不可或缺的，因此您應該將它們保留為選取狀態。

如果您使用以 OU 為基礎的篩選搭配1.1.524.0 版之前 Azure AD Connect 的版本，則預設會同步處理新的 Ou。 如果您不想要同步處理新的 Ou，則可以在以 [OU 為基礎的篩選](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) 步驟之後調整預設行為。 針對 Azure AD Connect 1.1.524.0 版或更新版本，您可以指定是否要同步處理新的 Ou。

如果您打算使用以 [群組為基礎的篩選](#sync-filtering-based-on-groups)，請確定包含群組的 OU 包含，而且未使用 OU 篩選進行篩選。 在評估以群組為基礎的篩選之前，會先評估 OU 篩選。

因為防火牆限制，某些網域也可能無法連線。 這些網域預設為未選取狀態，而且會顯示警告。  

![顯示無法連線之網域的螢幕擷取畫面。](./media/how-to-connect-install-custom/unreachable.png)  

如果您看到此警告，請確定這些網域確實無法連線，而且預期會出現警告。

### <a name="uniquely-identifying-your-users"></a>唯一識別您的使用者

在 [ **識別使用者** ] 頁面上，選擇如何識別內部部署目錄中的使用者，以及如何使用 sourceAnchor 屬性來識別它們。

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>選取在內部部署目錄中要如何識別使用者
藉由使用 *跨* 樹系的相符功能，您可以定義 Azure AD DS 樹系中的使用者如何以 Azure AD 表示。 使用者在所有樹系中可能只會顯示一次，或可能具有已啟用和已停用帳戶的組合。 使用者也可能顯示為某些樹系中的連絡人。

![顯示您可以用來唯一識別使用者之頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/unique2.png)

| 設定 | 描述 |
| --- | --- |
| [使用者只會在所有樹系中表示一次](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |在 Azure AD 中，所有使用者會都建立為個別物件。 這些物件不會聯結到元（元）。 |
| [郵件屬性](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |如果郵件屬性在不同樹系中具有相同的值，則此選項就會聯結使用者和連絡人。 當您的連絡人是使用 GALSync 建立時，請使用此選項。 如果您選擇此選項，則擴展郵件屬性的使用者物件不會同步處理至 Azure AD。 |
| [ObjectSID 和 msExchangeMasterAccountSID/Msrtcsip-primaryuseraddress-OriginatorSID 屬性](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |此選項會聯結帳戶樹系中已啟用的使用者與資源樹系中已停用的使用者。 在 Exchange 中，此組態稱為連結信箱。 如果您只使用 Lync，且資源樹系中沒有 Exchange，則可以使用此選項。 |
| SAMAccountName 和 MailNickName 屬性 |此選項會聯結應找到使用者登入識別碼的屬性。 |
| 選擇特定屬性 |此選項可讓您選取您的屬性。 如果您選擇此選項，則擴展選取的 () 屬性的使用者物件不會同步處理至 Azure AD。 **限制：** 只有已在元屬性中的屬性可用於這個選項。 |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>選取如何使用來源錨點識別使用者
*SourceAnchor*屬性在使用者物件的存留期間是不可變的。 它是將內部部署使用者與 Azure AD 中的使用者連結的主要金鑰。

| 設定 | 描述 |
| --- | --- |
| 讓 Azure 管理來源錨點 | 如果您想要 Azure AD 為您挑選屬性，請選取此選項。 如果您選取此選項，Azure AD Connect 會套用 [使用 ConsistencyGuid 做為 sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)所描述的 sourceAnchor 屬性選取邏輯。 自訂安裝完成之後，您會看到選取哪一個屬性做為 sourceAnchor 屬性。 |
| 選擇特定屬性 | 如果您想要將現有的 AD 屬性指定為 sourceAnchor 屬性，請選取此選項。 |

因為無法變更 sourceAnchor 屬性，所以您必須選擇適當的屬性。 objectGUID 就是不錯的選項。 除非在樹系或網域之間移動使用者帳戶，否則此屬性不會變更。 請勿選擇當人員結婚或變更指派時可以變更的屬性。 

您無法使用包含 @ 符號 ( @ ) 的屬性，因此無法使用電子郵件和 userPrincipalName。 屬性也會區分大小寫，因此當您在樹系間移動物件時，請務必保留大寫和小寫。 二進位屬性是以 Base64 編碼，但其他屬性類型仍維持未編碼的狀態。 

在同盟案例和某些 Azure AD 介面中，sourceAnchor 屬性也稱為 *immutableID*。 

如需來源錨點的詳細資訊，請參閱 [設計概念](plan-connect-design-concepts.md#sourceanchor)。

### <a name="sync-filtering-based-on-groups"></a>根據群組進行同步處理篩選
篩選群組功能可讓您只同步處理一小部分的物件來進行試驗。 若要使用這項功能，請在 Active Directory 的內部部署實例中建立此用途的群組。 然後新增應該同步處理至 Azure AD 做為直接成員的使用者和群組。 您稍後可以新增使用者或從此群組移除使用者，以維護 Azure AD 中應存在的物件清單。 

您要同步處理的所有物件都必須是群組的直接成員。 使用者、群組、連絡人和電腦或裝置都必須是直接成員。 未解析嵌套群組成員資格。 當您將群組新增為成員時，只會新增群組本身。 其成員不會加入。

![顯示頁面的螢幕擷取畫面，您可以在其中選擇篩選使用者和裝置的方式。](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> 這項功能的目的只是為了支援試驗部署。 請勿在完整的生產部署中使用。
>

在完整的生產部署中，很難維護單一群組及其所有物件來進行同步處理。 使用 [ [設定篩選](how-to-connect-sync-configure-filtering.md)] 中所述的其中一個方法，而不是 [篩選群組] 功能。

### <a name="optional-features"></a>選用功能
在下一個頁面上，您可以為您的案例選取選擇性功能。

>[!WARNING]
>Azure AD Connect 版本1.0.8641.0 版和更早版本會依賴 Azure 存取控制服務來進行密碼回寫。  此服務已于2018年11月7日淘汰。  如果您使用其中任何一種版本的 Azure AD Connect 並已啟用密碼回寫，則在服務淘汰時，使用者可能會失去變更或重設其密碼的能力。 這些版本的 Azure AD Connect 不支援密碼回寫。
>
>如需詳細資訊，請參閱 [從 Azure 存取控制服務遷移](../azuread-dev/active-directory-acs-migration.md)。
>
>如果您想要使用密碼回寫，請下載 [最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

![顯示 [選用功能] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> 如果 Azure AD 同步或直接同步處理 (DirSync) 為使用中，則不會啟用 Azure AD Connect 中的任何回寫功能。



| 選用功能 | 描述 |
| --- | --- |
| Exchange 混合式部署 |Exchange 混合式部署功能可讓您在內部部署和 Microsoft 365 中共存 Exchange 信箱。 Azure AD Connect 會將一組特定的 [屬性](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) 從 Azure AD 同步處理回您的內部部署目錄。 |
| Exchange 郵件公用資料夾 | Exchange 郵件公用資料夾功能可讓您將擁有郵件功能的公用資料夾物件從 Active Directory 的內部部署實例同步處理到 Azure AD。 |
| Azure AD 應用程式和屬性篩選 |藉由啟用 Azure AD 應用程式和屬性篩選，您可以自訂一組已同步處理的屬性。 這個選項會在精靈中另外新增兩個組態頁面。 如需詳細資訊，請參閱 [Azure AD 應用程式和屬性篩選](#azure-ad-app-and-attribute-filtering)。 |
| 密碼雜湊同步處理 |如果您選取 [同盟] 作為登入解決方案，您可以啟用密碼雜湊同步處理。 然後，您可以使用它做為備份選項。  </br></br>如果您選取了傳遞驗證，可以啟用此選項以確保支援舊版用戶端並提供備份。</br></br> 如需詳細資訊，請參閱 [密碼雜湊同步](how-to-connect-password-hash-synchronization.md)處理。|
| 密碼回寫 |您可以使用此選項來確保 Azure AD 中產生的密碼變更會寫回至內部部署目錄。 如需詳細資訊，請參閱[開始使用密碼管理](../authentication/tutorial-enable-sspr.md)。 |
| 群組回寫 |如果您使用 Microsoft 365 群組，則可以在 Active Directory 的內部部署實例中代表群組。 只有當您在 Active Directory 的內部部署實例中有 Exchange 時，才能使用此選項。 如需詳細資訊，請參閱 [Azure AD Connect 群組回寫](how-to-connect-group-writeback.md)。|
| 裝置回寫 |針對條件式存取案例，請使用此選項將 Azure AD 中的裝置物件寫回 Active Directory 的內部部署實例。 如需詳細資訊，請參閱[在 Azure AD Connect 中啟用裝置回寫](how-to-connect-device-writeback.md)。 |
| 目錄擴充屬性同步處理 |選取此選項可將指定的屬性同步至 Azure AD。 如需詳細資訊，請參閱[目錄擴充](how-to-connect-sync-feature-directory-extensions.md)。 |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD 應用程式和屬性篩選
如果您想要限制哪些屬性會同步處理至 Azure AD，請從選取您使用的服務開始。 如果您變更此頁面上的選項，您必須重新執行安裝精靈來明確選取新的服務。

![顯示選用的 Azure A D apps 功能的螢幕擷取畫面。](./media/how-to-connect-install-custom/azureadapps2.png)

根據您在上一個步驟中選取的服務，此頁面會顯示所有已同步處理的屬性。 這份清單是所有已同步處理之物件類型的組合。 如果您需要某些屬性維持未同步處理狀態，您可以從這些屬性清除選取專案。

![顯示選用 Azure A D 屬性功能的螢幕擷取畫面。](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> 移除屬性可能會影響功能。 如需最佳作法和建議，請參閱 [要同步處理的屬性](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize)。
>

### <a name="directory-extension-attribute-sync"></a>目錄擴充屬性同步處理
您可以使用組織新增的自訂屬性，或使用 Active Directory 中的其他屬性，來擴充 Azure AD 中的架構。 若要使用這項功能，請在 [ **選用功能** ] 頁面上，選取 [ **目錄擴充屬性同步**處理]。在 [ **目錄擴充** 功能] 頁面上，您可以選取多個要同步處理的屬性。

>[!NOTE]
>[ **可用的屬性** ] 欄位會區分大小寫。

![顯示 [目錄延伸] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/extension2.png)

如需詳細資訊，請參閱[目錄擴充](how-to-connect-sync-feature-directory-extensions.md)。

### <a name="enabling-single-sign-on"></a>啟用單一登入
在 [ **單一登入** ] 頁面上，您可以設定單一登入以用於密碼同步處理或傳遞驗證。 您可以針對每個同步處理至 Azure AD 的樹系執行此步驟一次。 設定包含兩個步驟：

1.  在 Active Directory 的內部部署實例中建立必要的電腦帳戶。
2.  設定用戶端電腦的內部網路區域，以支援單一登入。

#### <a name="create-the-computer-account-in-active-directory"></a>在 Active Directory 中建立電腦帳戶
針對已在 Azure AD Connect 中新增的每個樹系，您必須提供網域系統管理員認證，才能在每個樹系中建立電腦帳戶。 認證只會用來建立帳戶。 它們不會儲存或用於任何其他作業。 在 [ **啟用單一登入** ] 頁面上新增認證，如下圖所示。

![顯示 [啟用單一登入] 頁面的螢幕擷取畫面。 加入樹系認證。](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>您可以略過不想使用單一登入的樹系。

#### <a name="configure-the-intranet-zone-for-client-machines"></a>設定用戶端電腦的內部網路區域
若要確保用戶端在內部網路區域中自動登入，請確定 URL 是內部網路區域的一部分。 此步驟可確保加入網域的電腦會在連線到公司網路時，自動將 Kerberos 票證傳送至 Azure AD。

在具有群組原則管理工具的電腦上：

1.  開啟群組原則管理工具。
2.  編輯將套用至所有使用者的群組原則。 例如，預設網域原則。
3.  移至 [**使用者**設定  >  **系統管理範本**  >  **Windows 元件**]  >  **Internet Explorer**[  >  **網際網路主控台**  >  **安全性] 頁面**。 然後選取 [指派網站到區域清單]****。
4.  啟用原則。 然後，在對話方塊中，輸入的值名稱 `https://autologon.microsoftazuread-sso.com` 和值 `1` 。 您的設定看起來應該如下圖所示。
  
    ![顯示內部網路區域的螢幕擷取畫面。](./media/how-to-connect-install-custom/sitezone.png)

6.  選取 [確定]**** 兩次。

## <a name="configuring-federation-with-ad-fs"></a>設定與 AD FS 同盟
只要按幾下滑鼠就可以設定 Azure AD Connect 的 AD FS。 開始之前，您需要：

* 適用于同盟伺服器的 Windows Server 2012 R2 或更新版本。 應啟用遠端系統管理。
* 適用于 Web 應用程式 Proxy 伺服器的 Windows Server 2012 R2 或更新版本。 應啟用遠端系統管理。
* 您要使用的同盟服務名稱的 TLS/SSL 憑證 (例如 sts.contoso.com) 。

>[!NOTE]
>您可以使用 Azure AD Connect 來更新 AD FS 伺服器陣列的 TLS/SSL 憑證，即使您不使用它來管理您的同盟信任。

### <a name="ad-fs-configuration-prerequisites"></a>AD FS 設定必要條件
若要使用 Azure AD Connect 來設定 AD FS 伺服器陣列，請確定已在遠端伺服器上啟用 WinRM。 請確定您已完成 [同盟必要條件](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration)中的其他工作。 此外，請確定您遵循 [Azure AD Connect 和同盟/WAP 伺服器](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) 資料表中所列的埠需求。

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>建立新的 AD FS 伺服器陣列或使用現有的 AD FS 伺服器陣列
您可以使用現有的 AD FS 伺服器陣列或建立新的伺服器陣列。 如果您選擇建立新的帳戶，您必須提供 TLS/SSL 憑證。 如果 TLS/SSL 憑證受到密碼保護，則系統會提示您提供密碼。

![顯示「A D F S 伺服器陣列」頁面的螢幕擷取畫面](./media/how-to-connect-install-custom/adfs1.png)

如果您選擇使用現有的 AD FS 伺服器陣列，您會看到可設定 AD FS 和 Azure AD 之間信任關係的頁面。

>[!NOTE]
>您可以使用 Azure AD Connect 來管理一個 AD FS 伺服器陣列。 如果您有現有的同盟信任，而在選取的 AD FS 伺服器陣列上設定 Azure AD，Azure AD Connect 從頭開始重新建立信任。

### <a name="specify-the-ad-fs-servers"></a>指定 AD FS 伺服器
指定您要安裝 AD FS 的伺服器。 您可以根據您的容量需求，新增一或多部伺服器。 設定此設定之前，請先將所有 AD FS 伺服器加入 Active Directory。 Web 應用程式 Proxy 伺服器不需要此步驟。 

Microsoft 建議安裝一部專門用於測試和試驗部署的 AD FS 伺服器。 初始設定之後，您可以再次執行 Azure AD Connect，以新增及部署更多伺服器以符合您的調整需求。

> [!NOTE]
> 設定此設定之前，請確定您的所有伺服器都已加入 Azure AD 網域。
>


![顯示 [同盟伺服器] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>指定 Web 應用程式 Proxy 伺服器
指定您的 Web 應用程式 Proxy 伺服器。 Web 應用程式 Proxy 伺服器部署在周邊網路中，面向于外部網路。 它支援來自外部網路的驗證要求。 您可以根據您的容量需求，新增一或多部伺服器。 

Microsoft 建議安裝單一 Web 應用程式 Proxy 伺服器，以進行測試和試驗部署。 初始設定之後，您可以再次執行 Azure AD Connect，以新增及部署更多伺服器以符合您的調整需求。 建議您擁有相等數目的 proxy 伺服器，以滿足內部網路的驗證。

> [!NOTE]
> - 如果您使用的帳戶不是 Web 應用程式 Proxy 伺服器上的本機系統管理員，則系統會提示您輸入系統管理員認證。
> - 在指定 Web 應用程式 Proxy 伺服器之前，請確定 Azure AD Connect 伺服器和 Web 應用程式 Proxy 伺服器之間有 HTTP/HTTPS 連線能力。
> - 確定 Web 應用程式伺服器和 AD FS 伺服器之間有 HTTP/HTTPS 連線，以允許驗證要求流動。
>


![顯示 [Web 應用程式 Proxy 伺服器] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/adfs3.png)

系統會提示您輸入認證，讓 web 應用程式伺服器可以建立 AD FS 伺服器的安全連線。 這些認證必須是 AD FS 伺服器上的本機系統管理員帳戶。

![顯示 [認證] 頁面的螢幕擷取畫面。 系統管理員認證會在 [使用者名稱] 欄位和 [密碼] 欄位中輸入。](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>指定 AD FS 服務的服務帳戶
AD FS 服務需要網域服務帳戶來驗證使用者，並在 Active Directory 中查詢使用者資訊。 它可支援兩種類型的服務帳戶：

* **群組受管理的服務帳戶**：此帳戶類型是由 Windows Server 2012 引進 AD DS。 這種類型的帳戶會提供如 AD FS 的服務。 這是單一帳戶，您不需要定期更新密碼。 如果您在 AD FS 伺服器所屬的網域中已經有 Windows Server 2012 網域控制站，請使用此選項。
* **網域使用者帳戶**：這種類型的帳戶會要求您提供密碼，並在過期時定期更新。 只有當您的 AD FS 伺服器所屬的網域中沒有 Windows Server 2012 網域控制站時，才能使用此選項。

如果您選取 [ **建立群組受管理的服務帳戶** ]，但 Active Directory 中從未使用過這項功能，請輸入您的企業系統管理員認證。 這些認證會用來啟動金鑰存放區，並在 Active Directory 中啟用這項功能。

> [!NOTE]
> Azure AD Connect 檢查 AD FS 服務是否已註冊為網域中 (SPN) 的服務主體名稱。  Azure AD DS 不允許同時註冊重複的 Spn。  如果找到重複的 SPN，您將無法繼續進行，直到移除 SPN 為止。

![顯示 [A D F S 服務帳戶] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>選取您要建立同盟的 Azure AD 網域
使用 **Azure AD 網域** ] 頁面，即可設定 AD FS 和 Azure AD 之間的同盟關聯性。 在這裡，您會設定 AD FS，以提供 Azure AD 的安全性權杖。 您也會設定 Azure AD，以信任此 AD FS 實例中的權杖。 

在此頁面上，您只能在初始安裝中設定單一網域。 您可稍後再次執行 Azure AD Connect 以設定其他網域。

![顯示「Azure A D 網域」頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>驗證所選取用於同盟的 Azure AD 網域
當您選取要建立同盟的網域時，Azure AD Connect 提供可用來驗證未驗證網域的資訊。 如需詳細資訊，請參閱 [新增和驗證網域](../fundamentals/add-custom-domain.md)。

![顯示「Azure A D 網域」頁面的螢幕擷取畫面，包括您可以用來驗證網域的資訊。](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect 嘗試在設定階段驗證網域。 如果您未新增必要的網域名稱系統 (DNS) 記錄，則無法完成設定。
>


## <a name="configuring-federation-with-pingfederate"></a>設定與 PingFederate 的同盟
只要按幾下滑鼠，就可以 Azure AD Connect 設定 PingFederate。 需要下列必要條件：
- PingFederate 8.4 或更新版本。  如需詳細資訊，請參閱 [PingFederate 與 Azure Active Directory 整合和 Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html)。
- 您要使用的同盟服務名稱的 TLS/SSL 憑證 (例如 sts.contoso.com) 。

### <a name="verify-the-domain"></a>驗證網域
當您選擇使用 PingFederate 來設定同盟之後，系統會要求您確認您想要建立同盟的網域。  從下拉式功能表中選取網域。

![顯示「Azure A D 網域」頁面的螢幕擷取畫面。 已選取 "contoso.com" 範例定義域。](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>匯出 PingFederate 設定


將 PingFederate 設定為每個同盟 Azure 網域的同盟伺服器。  選取 [ **匯出設定** ]，以與您的 PingFederate 系統管理員共用此資訊。  同盟伺服器管理員會更新設定，然後提供 PingFederate 伺服器 URL 和埠號碼，讓 Azure AD Connect 可以確認中繼資料設定。  

![顯示 [PingFederate 設定] 頁面的螢幕擷取畫面。 [匯出設定] 按鈕會出現在頁面頂端附近。](./media/how-to-connect-install-custom/ping2.png)

若有任何驗證問題，請連絡您的 PingFederate 管理員加以解決。  下圖顯示與 Azure 沒有有效信任關係之 PingFederate 伺服器的相關資訊。

![顯示伺服器資訊的螢幕擷取畫面：找到 PingFederate 伺服器，但 Azure 的服務提供者連線已遺失或已停用。](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>驗證同盟連線能力
Azure AD Connect 嘗試驗證從上一個步驟中 PingFederate 中繼資料所抓取的驗證端點。  Azure AD Connect 第一次嘗試使用您的本機 DNS 伺服器來解析端點。  接下來，它會嘗試使用外部 DNS 提供者來解析端點。  若有任何驗證問題，請連絡您的 PingFederate 管理員加以解決。  

![顯示 [驗證連線能力] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>驗證同盟登入
最後，您可以藉由登入同盟網域，來驗證新設定的同盟登入流程。 如果您的登入成功，則已成功設定與 PingFederate 的同盟。

![顯示 [驗證同盟登入] 頁面的螢幕擷取畫面。 底部的訊息表示成功登入。](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>設定並確認頁面
設定會出現在 [ **設定** ] 頁面上。

> [!NOTE]
> 如果您已設定同盟，則在繼續安裝之前，請確定您也已設定 [同盟伺服器的名稱解析](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) 。
>



![顯示 [準備設定] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>使用預備模式
您可以使用預備模式來平行設定新的同步伺服器。 如果您想要使用此設定，則只有一部同步伺服器可以匯出至雲端中的一個目錄。 但是，如果您想要從另一部伺服器（例如執行 DirSync 的伺服器）進行移動，您可以在預備模式中啟用 Azure AD Connect。 

當您啟用預備設定時，同步處理引擎會正常匯入和同步處理資料。 但它不會將資料匯出至 Azure AD 或 Active Directory。 在預備模式中，會停用密碼同步功能和密碼回寫功能。

![顯示 [啟用暫存模式] 選項的螢幕擷取畫面。](./media/how-to-connect-install-custom/stagingmode.png)

在預備模式中，您可以對同步處理引擎進行必要的變更，並檢查將匯出的內容。 當此組態看起來設定良好時，請再次執行安裝精靈，並停用預備模式。 

資料現在會從伺服器匯出至 Azure AD。 同時請務必停用其他伺服器，如此才能只讓一部伺服器主動匯出。

如需詳細資訊，請參閱[預備模式](how-to-connect-sync-staging-server.md)。

### <a name="verify-your-federation-configuration"></a>驗證同盟組態
當您選取 [ **驗證** ] 按鈕時，Azure AD Connect 會驗證 DNS 設定。 它會檢查下列設定：

* **內部網路連線能力**
    * 解析同盟 FQDN： Azure AD Connect 檢查 DNS 是否可以解析同盟 FQDN，以確保連線能力。 如果 Azure AD Connect 無法解析 FQDN，則驗證會失敗。 若要完成驗證，請確定 federation service FQDN 有 DNS 記錄。
    * DNS A 記錄： Azure AD Connect 檢查您的同盟服務是否有 A 記錄。 如果沒有 A 記錄，驗證就會失敗。 若要完成驗證，請建立 A 記錄， (不是您同盟 FQDN 的 CNAME 記錄) 。
* **外部網路連線能力**
    * 解析同盟 FQDN： Azure AD Connect 檢查 DNS 是否可以解析同盟 FQDN，以確保連線能力。

      ![顯示 [安裝完成] 頁面的螢幕擷取畫面。](./media/how-to-connect-install-custom/completed.png)

      ![顯示 [安裝完成] 頁面的螢幕擷取畫面。 訊息指出已驗證內部網路設定。](./media/how-to-connect-install-custom/adfs7.png)

若要驗證端對端驗證，請手動執行下列一或多項測試：

* 當同步處理完成時，請在 Azure AD Connect 中，使用「 **驗證同盟登** 入」額外工作來確認您所選擇的內部部署使用者帳戶驗證。
* 從內部網路上已加入網域的電腦，確定您可以從瀏覽器登入。 連接到 https://myapps.microsoft.com。 然後使用您登入的帳戶來驗證登入。 內建的 Azure AD DS 系統管理員帳戶未進行同步處理，您無法使用它來進行驗證。
* 確定您可以從外部網路上的裝置登入。 在家庭電腦或行動裝置上，連接至 https://myapps.microsoft.com 。 然後提供您的認證。
* 驗證豐富型用戶端登入。 連接到 https://testconnectivity.microsoft.com。 然後選取 [ **office 365**  >  **office 365 單一 Sign-On 測試**]。

## <a name="troubleshoot"></a>疑難排解
如果您在安裝 Azure AD Connect 時發生問題，本節包含可供您使用的疑難排解資訊。

當您自訂 Azure AD Connect 安裝時，您可以在 [ **安裝必要元件** ] 頁面上選取 [ **使用現有的 SQL Server**。 您可能會看到下列錯誤：「ADSync 資料庫已經包含資料，且無法覆寫。 請移除現有的資料庫，然後再試一次。」

![顯示 [安裝必要元件] 頁面的螢幕擷取畫面。 頁面底部會出現錯誤。](./media/how-to-connect-install-custom/error1.png)

您會看到此錯誤，因為您指定的 SQL Server 的 SQL 實例上已有名為 *ADSync* 的資料庫。

當您卸載 Azure AD Connect 之後，通常會看到此錯誤。  當您卸載 Azure AD Connect 時，資料庫不會從執行 SQL Server 的電腦中刪除。

若要修正這個問題：

1. 檢查 Azure AD Connect 在卸載之前使用的 ADSync 資料庫。 請確定資料庫已不再使用。

2. 備份資料庫。

3. 刪除資料庫：
    1. 使用 **Microsoft SQL Server Management Studio** 連接到 SQL 實例。 
    1. 尋找 **ADSync** 資料庫，並在其上按一下滑鼠右鍵。
    1. 在內容功能表上，選取 [ **刪除**]。
    1. 選取 **[確定]** 以刪除資料庫。

![顯示 Microsoft SQL Server Management Studio 的螢幕擷取畫面。 已選取 D 個同步。](./media/how-to-connect-install-custom/error2.png)

刪除 ADSync 資料庫之後，請選取 [ **安裝** ] 以重試安裝。

## <a name="next-steps"></a>後續步驟
安裝完成之後，請登出 Windows。 然後使用 Synchronization Service Manager 或同步處理規則編輯器，重新登入。

現在您已安裝 Azure AD Connect，可以 [確認安裝並指派授權](how-to-connect-post-installation.md)。

如需有關您在安裝期間啟用之功能的詳細資訊，請參閱 [防止意外刪除](how-to-connect-sync-feature-prevent-accidental-deletes.md) 和 [Azure AD Connect Health](how-to-connect-health-sync.md)。

如需其他常見主題的詳細資訊，請參閱 [Azure AD Connect 同步處理：](how-to-connect-sync-feature-scheduler.md) 排程器和整合您的內部部署身分識別 [與 Azure AD](whatis-hybrid-identity.md)。
