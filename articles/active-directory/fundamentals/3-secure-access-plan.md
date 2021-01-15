---
title: 建立可供外部存取 Azure Active Directory 的安全性方案
description: 規劃外部存取組織資源的安全性。
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
ms.openlocfilehash: 40738f8fcb14c48ccfe3bc7869e5176c4ab63165
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222325"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. 建立外部存取的安全性計畫 

既然您已 [決定外部存取所需的安全性狀態安全性](1-secure-access-posture.md) 狀態，並 [發現您目前](2-secure-access-current-state.md)的共同作業狀態，您可以建立外部使用者安全性和治理方案。 

此計畫應記載下列各項：

* 應針對存取進行分組的應用程式和其他資源。

* 適用于外部使用者的適當登入條件。 這些可能包括裝置狀態、登入位置、用戶端應用程式需求，以及使用者風險。

* 檢查和移除存取權的商務原則。 

* 要分組並視為類似的使用者擴展。

一旦記載這些區域之後，您就可以使用 Microsoft 或任何其他身分識別提供者的身分識別與存取管理原則 (IdP) 來執行此計畫。

## <a name="document-resources-to-be-grouped-for-access"></a>要分組以進行存取的檔資源

有多種方式可將資源分組以進行存取。 

* Microsoft 小組會在同一個位置將檔案、對話執行緒和其他資源分組。 您應為 Microsoft 小組制訂外部存取策略。 查看對 [小組、OneDrive 和 SharePoint 的安全存取](9-secure-access-teams-sharepoint.md)。

* 權利管理存取套件可讓您建立單一應用程式套件，以及您可以授與存取權的其他資源。 

* 條件式存取原則可以套用至具有相同存取需求的250應用程式。

不過，您將會管理存取權，您必須記錄哪些應用程式應群組在一起。 考慮事項應包括：

* **風險設定檔**。 如果有不良執行者獲得應用程式的存取權，您的業務有何風險？ 請考慮以高、中或低風險撰寫每個應用程式的程式碼。 使用低風險的應用程式來分組高風險的應用程式時，請特別小心。 

   * 記錄不應該與外部使用者共用的應用程式。

* **合規性** 架構。 如果任何合規性架構必須符合應用程式，該怎麼辦？ 存取和審核需求有哪些？

* **特定工作角色或部門的應用程式**。 是否有應群組的應用程式，因為特定工作角色或部門中的所有使用者都需要存取權？

* **共同作業導向的應用程式**。 外部使用者應該能夠存取哪些共同作業導向的應用程式？ Microsoft 小組和 SharePoint 可能必須可以存取。 針對 Office 365 （例如 Word 和 Excel）內的生產力應用程式，外部使用者是否會攜帶自己的授權，或您是否需要授權並提供存取權？

針對您想要讓外部使用者存取的每個應用程式和資源群組，記錄下列各項：

* 群組的描述性名稱，例如 *High_Risk_External_Access_Finance*。 

* 群組中所有應用程式和資源的完整清單。

* 應用程式和資源擁有者和連絡人資訊。

* 存取權是由 IT 控制，還是委派給企業擁有者。

* 任何必要條件，例如完成背景檢查或訓練，以進行存取。

* 存取資源的任何合規性需求。

* 任何額外的挑戰，例如需要針對特定資源進行多重要素驗證。

* 存取權的檢查頻率，以及將會記載的位置。

這種類型的治理計畫也可以也必須完成，才能進行內部存取。

## <a name="document-sign-in-conditions-for-external-users"></a>適用于外部使用者的檔登入條件。

在您的方案中，您必須在存取資源時，判斷外部使用者的登入需求。 登入需求通常是根據資源的風險設定檔，以及使用者登入的風險評估。

登入條件是在 [Azure AD 條件式存取](../conditional-access/overview.md) 中設定，並由條件和結果組成。 例如，何時需要多重要素驗證

**以資源風險為基礎的登入條件。**

| 應用程式風險設定檔| 請考慮這些原則來觸發多重要素驗證 |
| - |-|
| 低風險| 針對特定應用程式集要求 MFA |
| Med-v 風險| 有其他風險時需要 MFA |
| 高風險| 外部使用者必須一律需要 MFA |


現在，您可以 [對租使用者中的 B2B 使用者強制執行多重要素驗證](https://docs.microsoft.com/azure/active-directory/external-identities/b2b-tutorial-require-mfa)。 

**以使用者和裝置為基礎的登入條件**。

| 使用者或登入風險| 考慮這些原則 |
| - | - |
| 裝置| 需要相容的裝置 |
| 行動應用程式| 需要已核准的應用程式 |
| Identity protection 顯示高風險| 要求使用者變更密碼 |
| 網路位置| 需要從特定 IP 位址範圍登入高度機密專案 |

現在，若要使用裝置狀態作為原則的輸入，裝置必須註冊或加入您的租使用者。 

您可以使用以身分[識別保護風險為基礎的原則](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)。 不過，使用者的主租使用者中必須降低問題。

針對 [網路位置](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-location)，您可以限制存取您擁有的任何 IP 位址範圍。 如果您只想要外部夥伴在您組織的網站上存取應用程式，則可以使用此資訊。

[深入瞭解條件式存取原則](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)。

## <a name="document-access-review-policies"></a>檔存取稽核原則

當您需要審查資源的存取權，以及當您需要移除外部使用者的帳戶存取權時，請記錄您的商務原則。 這些決策的輸入可能包括：

* 任何合規性架構中詳述的需求。

* 內部商務原則和流程

* 使用者行為

雖然您的原則會根據您的需求高度自訂，但請考慮下列事項：

* **權利管理存取權審核**。 使用權利管理中的功能

   * [會自動使存取套件過期](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-lifecycle-policy)，並讓外部使用者存取包含的資源。

   * 設定存取評論的 [必要審核頻率](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-reviews-create) 。

   * 如果您要使用 [已連線的組織](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-organization) 將單一夥伴的所有使用者分組，請以商務擁有者和合作夥伴代表排程定期審核。

* **Microsoft 365 群組**。 針對要邀請外部使用者的 Microsoft 365 群組設定 [群組到期原則](https://docs.microsoft.com/microsoft-365/solutions/microsoft-365-groups-expiration-policy?view=o365-worldwide) 。 

* **其他選項**。 如果外部使用者具有權利管理存取套件或 Microsoft 365 群組以外的存取權，請設定商務程式，以在帳戶應設為非使用中或已刪除時進行審查。 例如：

   * 移除任何未登入至90天之帳戶的登入功能。

   * 在每個專案結束時，使用外部使用者來評定存取需求和採取動作。

 

## <a name="determine-your-access-control-methods"></a>判斷您的存取控制方法

現在您已知道您想要控制存取權、如何將這些資產分組以進行一般存取，以及必要的登入和存取稽核原則，您可以決定如何完成您的計畫。 

某些功能（例如 [權利管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)）僅適用于 Azure AD Premium 2 (P2) 授權。 Microsoft 365 E5 和 Office 365 E5 授權包含 Azure AD P2 授權。 

Microsoft 365、Office 365 和 Azure AD 的其他組合也可提供管理外部使用者的部分功能。 如需詳細資訊，請參閱 [資訊保護](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) 。

> [!NOTE]
> 授權為每位使用者。 因此，您可以在 Azure AD P2 或 Microsoft 365 E5 層級，讓特定使用者（包括系統管理員和企業擁有者）委派存取控制，而不需要為所有使用者啟用這些授權。 您的第一個50000外部使用者是免費的。 如果您未為其他內部使用者啟用 P2 授權，他們將無法使用權利管理功能，例如存取套件。 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>使用 Azure AD P2 和 Microsoft/Office 365 E5 來管理存取權
Azure AD P2 和 Microsoft 365 E5 都有一套完整的安全性和治理工具。

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>布建、登入、檢查存取權，以及取消布建。 粗體專案是慣用的方法

| 功能| 提供外部使用者| 強制登入先決條件。| 檢閱存取| 取消布建存取權 |
| - | - | - | - | - |
| Azure AD B2B 共同作業| 透過電子郵件、OTP、自助服務邀請| | **每個合作夥伴的定期審核**| 移除帳戶<br>限制登入 |
| 權利管理| **透過指派或自助存取新增使用者**| | 存取權檢閱|**存取套件的到期或移除**|
| Office 365 群組| | | 審查群組成員資格| 群組的到期或刪除<br> 移除表單群組 |
| Azure AD 安全性群組| | **條件式存取原則** (視需要將外部使用者新增至安全性群組) | |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>資源的存取權。 粗體專案是慣用的方法

|功能 | 應用程式 & 資源存取| SharePoint & OneDrive 存取權| 小組存取| 電子郵件 & 檔安全性 |
| - |-|-|-|-|
| 權利管理| **透過指派或自助存取新增使用者**| **存取套件**| **存取套件**|  |
| Office 365 群組| | 存取網站 (s)  (和相關聯的內容) 包含在群組中| 存取小組 (以及群組中包含的相關內容) |  |
| 敏感度標籤| | **手動並自動分類和限制存取**| **手動並自動分類和限制存取**| **手動並自動分類和限制存取** |
| Azure AD 安全性群組| **存取套件中未包含存取的條件式存取原則**| | |  |


### <a name="entitlement-management"></a>權利管理 

[權利管理存取套件](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-create) 可讓您布建和解除布建群組和小組、應用程式和 SharePoint 網站的存取權。 您可以定義允許存取哪些已連線的組織、是否允許自助服務要求，以及需要哪些核准工作流程 (如果有任何) 授與存取權。 若要確保存取的時間不會超過所需的時間，您可以定義每個存取套件的到期原則和存取權評論。 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>使用 Azure AD P1 和 Microsoft/Office 365 E3 管理存取權
您可以使用 Azure AD P1 和 Microsoft 365 E3 達成穩固的治理

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>布建、登入、檢查存取和取消布建


|功能 | 提供外部使用者| 強制登入需求| 檢閱存取| 取消布建存取權 |
| - |-|-|-|-|
| Azure AD B2B 共同作業| **透過電子郵件、OTP、自助服務邀請**| 直接 B2B 同盟| **每個合作夥伴的定期審核**| 移除帳戶<br>限制登入 |
| Microsoft 或 Office 365 群組| | | | 群組的到期或刪除。<br>從群組移除。 |
| 安全性群組| | **將外部使用者新增至安全性群組 (組織、小組、專案等 )**| |  |
| 條件式存取原則| | **外部使用者的登入條件式存取原則**| |  |


 ### <a name="access-to-resources"></a>資源的存取權。

|功能 | 應用程式 & 資源存取| SharePoint & OneDrive 存取權| 小組存取| 電子郵件 & 檔安全性 |
| - |-|-|-|-|
| Microsoft 或 Office 365 群組| | **存取網站 (s) 包含在群組 (和相關聯的內容)**|**存取 Microsoft 365 群組所包含的小組 (和相關聯的內容)**|  |
| 敏感度標籤| | 手動分類和限制存取| 手動分類並限制存取。| 手動分類以限制和加密 |
| 條件式存取原則| 存取控制的條件式存取原則| | |  |
| 其他方法| | 使用安全性群組來更精確地限制 SharePoint 網站存取。<br>不允許直接共用。| **限制小組內的外部邀請**|  |


### <a name="next-steps"></a>後續步驟

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。

1. [判斷外部存取的安全性狀態](1-secure-access-posture.md)

2. [探索您目前的狀態](2-secure-access-current-state.md)

3. 在這裡[建立治理計畫](3-secure-access-plan.md) (。 ) 

4. [使用群組進行安全性](4-secure-access-groups.md)

5. [轉換至 Azure AD B2B](5-secure-access-b2b.md)

6. [使用權利管理來保護存取權](6-secure-access-entitlement-managment.md)

7. [使用條件式存取原則保護存取](7-secure-access-conditional-access.md)

8. [使用敏感度標籤保護存取](8-secure-access-sensitivity-labels.md)

9. [安全存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)