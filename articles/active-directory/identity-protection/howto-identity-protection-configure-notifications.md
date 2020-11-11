---
title: Azure Active Directory Identity Protection 通知
description: 了解通知如何支援您的調查活動。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9536cf41add73f494bfff451c201d36e951864e3
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489483"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知

Azure AD Identity Protection 傳送兩種類型的自動通知電子郵件，協助您管理使用者風險和風險偵測：

- 「偵測到具有風險的使用者」電子郵件
- 每週精選文章電子郵件

本文同時提供您這兩種通知電子郵件的概觀。

## <a name="users-at-risk-detected-email"></a>「偵測到具有風險的使用者」電子郵件

當偵測到具有風險的帳戶時，Azure AD Identity Protection 會產生主旨為 **偵測到具有風險的使用者** 的電子郵件警示來作為回應。 此電子郵件會包含 **[標幟為有風險的使用者](./overview-identity-protection.md)** 報告的連結。 最佳做法是應立即調查具有風險的使用者。

此警示的設定可讓您指定要產生警示的使用者風險層級。 當使用者的風險層級達到您所指定的內容時，就會產生電子郵件。 例如，如果您將原則設定為 [中等使用者風險] 警示，而您的使用者 John 的使用者風險分數因為即時登入風險而移至 [中] 風險，您將會收到具有風險的使用者電子郵件。 如果使用者的後續風險偵測造成使用者風險層級的計算是指定的風險層級 (或更高的) ，則在重新計算使用者風險分數時，您將會收到額外的使用者有風險偵測到的電子郵件。 例如，如果使用者在1月1日移至中度風險，而您的設定設定為 [中度風險] 的警示，您將會收到電子郵件通知。 如果同一個使用者在1月5日也有另一個風險偵測，也就是中度風險，而使用者風險分數已重新計算，而且仍是「中」，則您會收到另一封電子郵件通知。 

不過，只有在發生風險偵測 (造成使用者風險層) 級變更的時間，而不是最後傳送電子郵件時，才會傳送額外的電子郵件通知。 例如，使用者在1月1日上午5點登入，並沒有即時風險 (表示因為登入) ，所以不會產生任何電子郵件。 10分鐘5:10 之後，相同的使用者再次登入並有高即時風險，會導致使用者風險層級移至高，並傳送電子郵件給您。 然後，在上午5:15，原始登入的離線風險分數（上午5點）會因為離線風險處理而變更為高風險。 因為第一次登入的時間早于第二次登入已觸發電子郵件通知的時間，所以不會傳送額外的使用者標示為風險電子郵件。

為了避免電子郵件超載，您只會在5秒的時間內收到一封電子郵件。 此延遲表示如果有多個使用者在相同的5秒期間內移至指定的風險層級，我們將會匯總並傳送一封電子郵件，以代表所有這些使用者的風險層級變更。

如果您的組織已啟用自動修復（如文章所述）， [使用者體驗的 Azure AD Identity Protection](concept-identity-protection-user-experience.md) 可能會讓使用者在您有機會進行調查之前補救其風險。 您可以在具風險的 **使用者** 或有風險的登 **入** 報告中新增「補救」至 **風險狀態** 篩選，以查看已補救的具風險使用者和有風險的登入。

![「偵測到具有風險的使用者」電子郵件](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>設定有風險的使用者偵測警示

如果您是系統管理員，您可以設定：

- **觸發這封電子郵件產生的使用者風險層** 級-依預設，風險層級會設定為「高」風險。
- **此電子郵件** 的收件者（全域管理員、安全性系統管理員或安全性讀取者角色中的使用者）會自動新增到此清單中。 我們嘗試將電子郵件傳送給每個角色的前20名成員。 如果使用者在 PIM 中註冊為視需要提升為這些角色的其中一個，則 **只有在電子郵件傳送時提高許可權時，才會收到電子** 郵件。
   - （選擇性）您可以在 **這裡新增自訂電子郵件** ，使用者必須具備適當的許可權，才能在 Azure 入口網站中查看連結的報表。

在 [安全性身分 **Azure portal** **Azure Active Directory**  >  **Security**  >  **識別保護**  >  **使用者有風險** 偵測的警示] Azure Active Directory 下的 Azure 入口網站中，設定具有風險的使用者電子郵件。

## <a name="weekly-digest-email"></a>每週精選文章電子郵件

每週摘要電子郵件包含新風險偵測的摘要。  
其中包括：

- 偵測到新的具風險使用者
-  (即時偵測到新的具風險登入) 
- Identity Protection 中相關報告的連結

![每週精選文章電子郵件](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

全域管理員、安全性系統管理員或安全性讀取者角色中的使用者會自動新增到此清單中。 我們嘗試將電子郵件傳送給每個角色的前20名成員。 如果使用者在 PIM 中註冊為視需要提升為這些角色的其中一個，則 **只有在電子郵件傳送時提高許可權時，才會收到電子郵件**

### <a name="configure-weekly-digest-email"></a>設定每週摘要電子郵件

以系統管理員身分，您可以開啟或關閉傳送每週摘要電子郵件的功能，並選擇獲指派以接收電子郵件的使用者。

在 [ **Azure 入口網站** **Azure Active Directory**  >  **安全性** 身分  >  **識別保護** 的  >  **每週摘要** ] 下，設定中的每週摘要電子郵件。

## <a name="see-also"></a>請參閱

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
