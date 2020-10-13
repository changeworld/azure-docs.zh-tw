---
title: Microsoft 365 外部共用和 B2B 共同作業-Azure AD
description: 討論使用 Microsoft 365 和 Azure Active Directory B2B 共同作業，與外部合作夥伴共用資源。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563d925aa3e0d6922756ab037226f5a86534b19f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705430"
---
# <a name="microsoft-365-external-sharing-and-azure-active-directory-azure-ad-b2b-collaboration"></a>Microsoft 365 的外部共用和 Azure Active Directory (Azure AD) B2B 共同作業

Azure AD B2B 共同作業和 Microsoft 365 外部共用 (OneDrive、SharePoint Online、整合群組等 ) ，則會使用 Azure AD B2B 來驗證外部使用者。

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Azure AD B2B 與 SharePoint Online 中的外部共用有何不同？

OneDrive/SharePoint Online 有個別的邀請管理員。 OneDrive/SharePoint Online 中的外部共用支援會在 Azure AD 開發其支援之前開始。 隨著時間的經過，OneDrive/SharePoint Online 外部共用已發展出數個功能，而且已有數百萬個使用者使用該產品的內建共用模式。 不過，OneDrive/SharePoint Online 外部共用的運作方式與 Azure AD B2B 共同作業的運作方式有些微差異。 您可以在[外部共用概觀](https://docs.microsoft.com/sharepoint/external-sharing-overview)中深入了解 OneDrive/SharePoint Online 外部共用。 此程序在下列各方面通常會與 Azure AD B2B 不同：

- OneDrive/SharePoint Online 會在使用者兌換其邀請之後，將使用者新增至目錄。 因此在兌換前，您不會在 Azure AD 入口網站中看到使用者。 如果其他網站同時邀請使用者，則會產生新的邀請。 不過，當您使用 Azure AD B2B 共同作業時，使用者會立即新增於邀請上，因而顯示在每個地方。

- OneDrive/SharePoint Online 中的兌換體驗與 Azure AD B2B 共同作業中的體驗有所差異。 在使用者兌換邀請之後，體驗看起來很類似。

- 您可以從 OneDrive/SharePoint Online 共用對話方塊挑選 Azure AD B2B 共同作業邀請的使用者。 在 OneDrive/SharePoint 邀請的使用者兌換其邀請之後，也會出現在 Azure AD 中。

- 授權需求不同。 對於每個付費的 Azure AD 授權，您最多可讓 5 位來賓使用者存取您的付費 Azure AD 功能。 若要深入了解授權，請參閱 [Azure AD B2B 授權](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance)以及[＜SharePoint Online 外部共用概觀＞中的「什麼是外部使用者？」](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share)。

若要使用 Azure AD B2B 共同作業來管理 OneDrive/SharePoint Online 中的外部共用，請將 OneDrive/SharePoint Online 外部共用設定設為 [只允許與您組織目錄中現有的外部使用者共用]****。 使用者可以移至外部共用網站，並從系統管理員新增的外部共同作業者之間選擇。 系統管理員可以透過 B2B 共同作業邀請 API 來新增外部共同作業者。


![OneDrive/SharePoint 外部共用設定](media/o365-external-user/odsp-sharing-setting.png)

在啟用外部共用之後，在 SharePoint Online (SPO) 人員選擇器中搜尋現有來賓使用者的功能預設為關閉，以符合舊版的行為。

您可以使用租用戶和網站集合層級中的 'ShowPeoplePickerSuggestionsForGuestUsers' 設定來啟用此功能。 您可以使用 Set-SPOTenant 和 Set-SPOSite Cmdlet 來設定此功能，讓成員搜尋目錄中所有的現有來賓使用者。 租用戶範圍中的變更不會影響已佈建的 SPO 網站。

## <a name="next-steps"></a>後續步驟

* [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
* [將 B2B 共同作業使用者新增至角色](add-guest-to-role.md)
* [委派 B2B 共同作業邀請](delegate-invitations.md)
* [動態群組與 B2B 共同作業](use-dynamic-groups.md)
* [針對 Azure Active Directory B2B 共同作業進行疑難排解](troubleshoot.md)
