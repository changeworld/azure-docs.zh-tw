---
title: 使用 Azure Active Directory 保護 Microsoft 小組、SharePoint 和 OneDrive 的外部存取
description: 安全地存取 Microsoft 365 服務，以作為整體外部存取安全性的一部分。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64a5bd409a29643c1a51630d6f029da7c90d29ad
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743904"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>保護 Microsoft 小組、SharePoint 和商務用 OneDrive 的外部存取 

Microsoft 小組、SharePoint 和商務用 OneDrive 是與外部使用者共同作業及共用內容的三種最常用方式。 如果「已核准」方法的限制太多，使用者將會透過電子郵件內容或設定不安全的外部程式和應用程式（例如個人 DropBox 或 OneDrive），在核准的方法之外離開。 您的目標是要在您的安全性需求與輕鬆共同作業之間取得平衡。

本文將引導您使用 Microsoft 小組和 SharePoint 來判斷及設定外部共同作業，以符合您的業務目標。

## <a name="governance-begins-in-azure-active-directory"></a>治理開始于 Azure Active Directory

Microsoft 365 中的共用屬於外部身分識別的一部分 [|](https://aad.portal.azure.com/) Azure Active Directory (Azure AD) 中的外部共同作業設定。 如果 Azure AD 中停用或限制外部共用，它會覆寫 Microsoft 365 中設定的任何共用設定。 例外狀況是，如果未啟用 Azure AD B2B 整合，則可以設定 SharePoint 和 OneDrive 以支援透過單次密碼 (OTP) 的臨機操作共用。

![外部共同作業設定的螢幕擷取畫面](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>來賓使用者存取權

來賓使用者存取有三個選擇，可控制來賓使用者在受邀之後可以看到的內容。 

若要防止來賓使用者查看其他來賓使用者的詳細資料，並能夠列舉群組成員資格，請選擇 [來賓使用者對目錄物件的屬性和成員資格的存取權有限]。

### <a name="guest-invite-settings"></a>來賓邀請設定

這些設定會決定誰可以邀請來賓，以及如何邀請來賓。 只有在已啟用與 B2B 的整合時，才會啟用這些設定。

建議您讓來賓邀請者角色中的系統管理員和使用者可以邀請。 這項設定可讓您設定受控制的共同作業進程，如下列範例所示。

* 小組擁有者提交要指派來賓邀請者角色的票證，以及

   * 會負責所有來賓邀請。

   * 同意不直接將使用者加入基礎 SharePoint

   * 負責執行定期存取審核，並視需要撤銷存取權。

* 中央 IT 會執行下列動作

   * 在定型完成時授與要求的角色，以啟用外部共用。

   * 將 Azure AD P2 授權指派給 Microsoft 365 群組擁有者，以啟用存取權審核。
   * 建立 Microsoft 365 群組存取權審核。

   * 確認正在進行存取檢查。

   * 移除直接加入基礎 SharePoint 中的使用者。

 **針對來賓 (預覽版) 啟用電子郵件單次密碼，並啟用透過使用者流程的來賓自助式簽署** 為 **是**。 這項設定會利用與 Azure AD 外部協同作業設定的整合。

### <a name="collaboration-restrictions"></a>共同作業限制

在共同作業限制下有三個選擇。 您的商務需求會決定您要選擇哪一種。

* **允許將邀請傳送至任何網域** 表示可以邀請任何使用者共同作業。

* **拒絕指定網域的邀請** ，表示這些使用者以外的任何使用者都可以受邀共同作業。

* **僅允許對指定網域的邀請** ，表示無法邀請這些指定網域以外的任何使用者。 

## <a name="govern-access-in-teams"></a>管理小組中的存取權

[小組會區分外部使用者 (組織外部的任何人) 和來賓使用者 (來賓帳戶) ](https://docs.microsoft.com/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH)) 。 您可以在 [小組系統管理員入口網站](https://admin.teams.microsoft.com/company-wide-settings/external-communications) 中，以全組織設定來管理協同作業設定。 

> [!NOTE]
> Azure Active Directory 中的外部身分識別共同作業設定會控制有效的許可權。 您可以增加小組的限制，但不能減少在 Azure AD 中設定的限制。

* **外部存取設定**。 依預設，小組允許外部存取，這表示組織可以與所有外部網域通訊。 如果您想要限制或只允許小組進行特定網域，您可以在這裡進行。

* **來賓存取**。 來賓存取控制來賓使用者可在小組中進行的動作。

若要深入瞭解如何管理小組的外部存取，請參閱下列資源。

* [管理 Microsoft 團隊的外部存取](https://docs.microsoft.com/microsoftteams/manage-external-access)

* [Microsoft 365 身分識別模型和 Azure Active Directory](https://docs.microsoft.com/microsoft-365/enterprise/about-microsoft-365-identity?view=o365-worldwide)

* [Microsoft 小組的身分識別模型與驗證](https://docs.microsoft.com/MicrosoftTeams/identify-models-authentication)

* [Microsoft 小組的敏感度標籤](https://docs.microsoft.com/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>在 SharePoint 和 OneDrive 中管理存取權

SharePoint 系統管理員有許多可供共同作業的設定。 整個組織的設定都是從 SharePoint 管理中心進行管理。 您可以調整每個 SharePoint 網站的設定。 建議您以最基本的必要安全性等級來設定整個組織的設定，而且您可以視需要提高特定網站的安全性。 例如，針對高風險的專案，您可能會想要限制使用者只能使用某些網域，並停用成員邀請來賓的能力。

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>將 SharePoint 和一個磁片磁碟機與 Azure AD B2B 整合

在您管理外部共同作業的整體策略中，我們建議您 [啟用 SharePoint 和 OneDrive 與 AZURE AD B2B 整合的預覽](https://docs.microsoft.com/sharepoint/sharepoint-azureb2b-integration-preview) 。

Azure AD B2B 提供來賓使用者的驗證和管理。 使用 SharePoint 和 OneDrive 整合時， [AZURE AD B2B 單次密碼](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode) 可用於檔案、資料夾、清單專案、文件庫和網站的外部共用。 這項功能提供來自現有 [安全外部共用收件者體驗](https://docs.microsoft.com/sharepoint/what-s-new-in-sharing-in-targeted-release)的升級體驗。

> [!NOTE]
> 如果您啟用 Azure AD B2B 整合的預覽，則 SharePoint 和 OneDrive 共用會受限於 Azure AD 的組織關係設定，例如 **成員可以邀請** ，以及 **來賓可以邀請**。

### <a name="sharing-policies"></a>共用原則

您可以同時設定 SharePoint 和 OneDrive 的 *外部共用*。 OneDrive 限制不能比 SharePoint 設定更寬鬆。

 ![SharePoint 和 OneDrive 中外部共用設定的螢幕擷取畫面](media/secure-external-access/9-sharepoint-settings.png)

SharePoint 與 Azure AD B2B 的整合變更了控制項與帳戶的互動方式。

* **任何人**。 不建議使用

   * 無論整合狀態為何，啟用任何人的連結表示使用此類型的連結時，不會套用任何 Azure 原則。 

   * 在控管共同作業的案例中，請勿啟用這項功能。 
   > [!NOTE]
   > 您可能會發現需要針對特定網站啟用此設定的案例，在此情況下，您會在這裡啟用它，並在個別網站上設定更大的限制。

* **新的和現有的來賓**。 如果您已啟用整合，則建議使用。

   * **在啟用 AZURE AD B2B 整合** 的情況下，新的和現有的來賓將具有可使用 Azure AD 原則管理的 Azure AD B2B 來賓帳戶。

   * 若 **未啟用 AZURE AD b2b 整合**，新的來賓將不會建立 Azure AD b2b 帳戶，且無法從 Azure AD 進行管理。 現有的來賓是否有 Azure AD B2B 帳戶取決於如何建立來賓帳戶。

* **現有的來賓**。 如果您未啟用整合，則建議使用。

   * 啟用此功能後，使用者只能與您目錄中已有的其他使用者共用。

* **僅限您組織中的人員**。 當您需要與外部使用者共同作業時，不建議使用此選項。

   * 無論整合狀態為何，使用者都只能與您組織中的使用者共用。 

* **限制依網域的外部共用**。 依預設，SharePoint 允許外部存取，這表示允許所有外部網域共用。 如果您想要限制或只允許 SharePoint 的特定網域，您可以在這裡進行。

* **僅允許特定安全性群組中的使用者共用外部**。  這項設定會限制誰可以共用 SharePoint 和 OneDrive 中的內容，而 Azure AD 中的設定適用于所有應用程式。 如果您想要讓使用者進行安全的共用訓練，並在完成時將其新增至核准的共用安全性群組，則限制可以共用的人員會很有用。 如果選取此設定，且使用者沒有方法可存取成為「核准的共用者」，他們可能會尋找未核准的共用方式。 

* **允許來賓共用他們未擁有的專案**。 建議您保持停用。

* **使用驗證碼的人員必須在這幾天後重新驗證 (預設值為 30)**。 建議您啟用此設定。

### <a name="access-controls"></a>存取控制

存取控制設定將會影響您組織中的所有使用者。 由於您可能無法控制外部使用者是否有相容的裝置，因此我們不會在此解決這些控制項。 

* **閒置會話登出**。建議您啟用此控制項，這可讓您在非使用狀態一段時間後，警告並登出非受控裝置上的使用者。 您可以設定非活動時段和警告。 

* **網路位置**。 設定此控制項，表示您可以只允許您的組織所擁有的 IP 位址存取形式。 在外部共同作業案例中，只有在所有外部夥伴都只會在您的網路中或透過您的 VPN 存取資源時，才設定此項。

### <a name="file-and-folder-links"></a>檔案和資料夾連結

在 SharePoint 系統管理中心，您也可以設定如何共用檔案和資料夾連結。 您也可以針對每個網站設定這些設定。 

 ![檔案和資料夾連結設定的螢幕擷取畫面](media/secure-external-access/9-file-folder-links.png)

如果您已啟用與 Azure AD B2B 的整合，與組織外部的檔案和資料夾共用，將會導致 B2B 使用者在共用檔案與資料夾時建立。

建議您將預設連結類型設定為 **僅限您組織中的人員**，以及要 **編輯** 的預設許可權。 這樣做可確保專案會 thoughtfully 共用。 然後，您可以針對符合特定共同作業需求的每個網站預設自訂此設定。

### <a name="anyone-links"></a>任何人連結

我們不建議您啟用任何人的連結。 如果您這麼做，建議您設定到期日，並考慮將其限制為 view 許可權。 如果您選擇 [僅查看檔案或資料夾的許可權]，使用者將無法變更任何人的連結，包括編輯許可權。

若要深入瞭解如何管理對 SharePoint 的外部存取，請參閱下列各項：

* [SharePoint 外部共用總覽](https://docs.microsoft.com/sharepoint/external-sharing-overview)

* [SharePoint 和 OneDrive 與 Azure AD B2B 整合](https://docs.microsoft.com/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>後續步驟

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。

1. [判斷外部存取的安全性狀態](1-secure-access-posture.md)

2. [探索您目前的狀態](2-secure-access-current-state.md)

3. [建立治理計畫](3-secure-access-plan.md)

4. [使用群組進行安全性](4-secure-access-groups.md)

5. [轉換至 Azure AD B2B](5-secure-access-b2b.md)

6. [使用權利管理來保護存取權](6-secure-access-entitlement-managment.md)

7. [使用條件式存取原則保護存取](7-secure-access-conditional-access.md)

8. [使用敏感度標籤保護存取](8-secure-access-sensitivity-labels.md)

9. 您可以在這裡[安全地存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md) (。 ) 