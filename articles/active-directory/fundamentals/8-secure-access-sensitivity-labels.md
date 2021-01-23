---
title: 使用敏感度標籤控制 Azure Active Directory 中資源的外部存取。
description: 在外部存取的整體安全性計畫中使用敏感度標籤。
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
ms.openlocfilehash: 0dcb6d24eacbe594a907f084874e76fea963c561
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725123"
---
# <a name="control-access-with-sensitivity-labels"></a>使用敏感度標籤來控制存取權 

[敏感度標籤](/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide) 可協助您控制 Office 365 應用程式中的內容存取，以及在 Microsoft 小組、Microsoft 365 群組和 SharePoint 網站等容器中的存取權。 他們可以保護您的內容，而不會阻礙使用者的共同作業和生產能力。 敏感度標籤可讓您在裝置、應用程式和服務之間傳送組織的內容，同時保護您的資料並符合您的合規性和安全性原則。 

使用敏感度標籤，您可以：

* **分類內容而不新增任何保護設定**。 您可以將分類指派給內容 (例如，在使用和共用內容時保存和漫遊的貼紙) 。 您可以使用此分類產生使用量報告，並查看敏感性內容的活動資料。

* **強制執行保護設定，例如加密、浮水印和存取限制**。 例如，使用者可以將機密標籤套用至檔或電子郵件，而且該標籤可以 [加密內容](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) 並新增「機密」浮水印。 此外，您可以 [將敏感度標籤套用至容器](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide) （例如 SharePoint 網站），並強制外部使用者是否可以存取它所包含的內容。

電子郵件上的敏感度標籤和其他內容的內容移動。 容器上的敏感度標籤可以限制對容器的存取，但是容器中的內容不會繼承標籤。 例如，使用者可以從受保護的網站取得內容、下載內容，然後在不受限制的情況下共用，除非內容也有敏感度標籤。

 >[!NOTE]
>若要套用敏感度標籤，使用者必須登入其 Microsoft 公司或學校帳戶。 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>建立及管理敏感度層級所需的許可權

將建立敏感度標籤的合規性小組成員，需要 Microsoft 365 合規性中心、Microsoft 365 的安全性中心，或安全性 & 合規性中心的許可權。

根據預設，您租使用者的全域管理員可以存取這些系統管理中心，並可讓合規性人員和其他人存取，而不需為他們提供租使用者系統管理員的擁有權限。針對此委派受限的系統管理員存取權，請將使用者新增至符合性資料管理員、合規性管理員或安全性系統管理員角色群組。

 

## <a name="determine-your-sensitivity-label-strategy"></a>判斷您的敏感度標籤策略

當您考慮管理內容的外部存取時，請決定下列各項：

**適用于所有內容和容器**

* 您將如何定義 (HBI、MBI、LBI) 的高、中或低業務影響？ 如果特定類型的內容以不當的共用，請考慮對組織的影響。

   * 具有特定類型之固有 [敏感性內容](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)的內容，例如信用卡或 passport 號碼

   * 特定群組或人員所建立的內容 (例如，合規性人員、財務人員或主管) 

   * 特定文件庫或網站中的內容。 例如，裝載組織策略或私用財務資料的容器

   * 其他準則

* 哪些類別的內容 (例如 HBI 內容) 應受限於外部使用者的存取權？

   * 限制可以包含限制對容器的存取，以及加密內容等動作。

* HBI 資料、網站或 Microsoft 365 群組應該具備哪些預設值？

* 您將使用敏感度標籤來 [標記和監視](/microsoft-365/compliance/label-analytics?view=o365-worldwide)，而不是 [強制加密](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) 或 [強制執行容器存取限制](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)？

**適用于電子郵件和內容**

* 您是否要 [自動將敏感度標籤](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) 套用至內容，或以手動方式進行？

   * 如果您選擇手動這麼做，您是否想要 [建議使用者套用標籤](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)？

**針對容器**

* 哪些準則會判斷 M365 群組、小組或 SharePoint 網站是否需要使用敏感度標籤來限制存取？

* 您只想要在這些容器中的內容向前移動，或是要在 SharePoint 和 OneDrive 中 [自動標記](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) 現有的檔案嗎？

請參閱這些 [常見的敏感度標籤案例](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide) ，以瞭解如何使用敏感度標籤的其他想法。

### <a name="sensitivity-labels-on-email-and-content"></a>電子郵件和內容上的敏感度標籤

當您將敏感度標籤指派給檔或電子郵件時，它就像是套用至可自訂、純文字和持續性內容的戳記。 

* 可 **自訂** 的意思是，您可以建立適合您組織的標籤，並決定套用時所發生的情況。

* **純文字** 表示它是專案中繼資料的一部分，而且可以由應用程式和服務讀取，讓他們可以套用自己的保護動作。

* **持續** 性表示標籤和任何相關聯的保護都會隨著內容漫遊，並成為套用和強制執行原則的基礎。

 

> [!NOTE]
> 每個內容專案都可以套用單一敏感度標籤。


### <a name="sensitivity-labels-on-containers"></a>容器上的敏感度標籤

您可以在容器（例如 [Microsoft 365 群組](../enterprise-users/groups-assign-sensitivity-labels.md)、 [Microsoft 小組](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)和 [SharePoint 網站](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)）上套用敏感度標籤。 當您將此敏感度標籤套用至支援的容器時，標籤會自動將分類和保護設定套用至連線的網站或群組。 這些容器上的敏感度標籤可以控制容器的下列層面：

* **隱私權**。 您可以選擇可以看到網站的人員：特定使用者、所有內部使用者或任何人。

* **外部使用者存取**。 控制群組擁有者是否可以將來賓新增至群組。

* **從未受管理的裝置進行存取**。 決定未受管理的裝置是否可以存取內容。

 

![編輯敏感度標籤的螢幕擷取畫面](media/secure-external-access/8-edit-label.png)

 

當您將敏感度標籤套用至容器（例如 SharePoint 網站）時，它不會套用至該處的內容：容器上的敏感度標籤會控制容器內內容的存取權。 

* 如果您想要自動將標籤套用至容器內的內容，請參閱 [自動將敏感度套用至內容](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)。

* 如果您想要讓使用者能夠手動將標籤套用到這個內容，請確定您已 [在 SharePoint 和 OneDrive 中啟用 Office 檔案的敏感度標籤](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)。

### <a name="plan-to-implement-sensitivity-labels"></a>規劃執行敏感度標籤

一旦您決定要如何使用敏感度標籤，以及您想要套用的內容和網站，請參閱下列檔，以協助您執行您的執行。

1. [開始使用敏感度標籤](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

2. [建立部署策略](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

3. [建立和發佈敏感度標籤](/microsoft-365/compliance/create-sensitivity-labels?view=o365-worldwide)

4. [使用敏感度標籤限制對內容的存取，以套用加密](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)

5. [搭配使用敏感度標籤與小組、群組和網站](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)

6. [在 SharePoint 和 OneDrive 中啟用 Office 檔案的敏感度標籤](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)

### <a name="next-steps"></a>後續步驟

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。

1. [判斷您所需的外部存取安全性狀態](1-secure-access-posture.md)

2. [探索您目前的狀態](2-secure-access-current-state.md)

3. [建立治理計畫](3-secure-access-plan.md)

4. [使用群組進行安全性](4-secure-access-groups.md)

5. [轉換至 Azure AD B2B](5-secure-access-b2b.md)

6. [使用權利管理來保護存取權](6-secure-access-entitlement-managment.md)

7. [使用條件式存取原則保護存取](7-secure-access-conditional-access.md)

8. [使用敏感度標籤](8-secure-access-sensitivity-labels.md) (您的安全存取。 ) 

9. [安全存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)