---
title: Azure Active Directory Identity Protection 通知
description: 了解通知如何支援您的調查活動。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9090ca5b8057179b0cbef1d0a87ae563303ed2c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130427"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知

Azure AD Identity Protection 會傳送兩種類型的自動化通知電子郵件，協助您管理使用者風險和風險偵測：

- 「偵測到具有風險的使用者」電子郵件
- 每週精選文章電子郵件

本文同時提供您這兩種通知電子郵件的概觀。

## <a name="users-at-risk-detected-email"></a>「偵測到具有風險的使用者」電子郵件

當偵測到具有風險的帳戶時，Azure AD Identity Protection 會產生主旨為**偵測到具有風險的使用者**的電子郵件警示來作為回應。 此電子郵件會包含**[標幟為有風險的使用者](../reports-monitoring/concept-user-at-risk.md)** 報告的連結。 最佳做法是應立即調查具有風險的使用者。

此警示的設定可讓您指定要產生警示的使用者風險層級。 當使用者的風險層級達到您指定的內容時，就會產生電子郵件。 例如，如果您將原則設定為針對中型使用者風險發出警示，而您的使用者 John 的使用者風險分數因為即時登入風險而移到中度風險，您會收到有風險偵測到的使用者電子郵件。 如果使用者後續的風險偵測導致使用者風險層級計算達到指定的風險等級（或更新版本），則當重新計算使用者風險分數時，您會收到額外的使用者有風險偵測到的電子郵件。 例如，如果使用者在1月1日移至中等風險，您的設定會收到一則電子郵件通知，表示設定為 [中風險] 的警示。 如果相同的使用者在1月5日具有另一個風險偵測，也就是「中」風險，而且使用者風險分數已重新計算且仍為「中」，則您會收到另一封電子郵件通知。 

不過，如果發生風險偵測（這會造成使用者風險層級變更）比最後一封電子郵件傳送的時間還新，則只會傳送額外的電子郵件通知。 例如，使用者在1月1日上午5點登入，而且不會有即時風險（表示由於該登入而不會產生任何電子郵件）。 10分鐘後5:10，相同的使用者再次登入並具有高即時風險，導致使用者風險層級移至高和傳送電子郵件。 然後，在上午5:15，原始登入在上午5點的離線風險分數會因為離線風險處理而變更為高風險。 因為第一次登入的時間早于已觸發電子郵件通知的第二個登入，所以不會傳送另一個標示為風險電子郵件的使用者。

為避免電子郵件超載，您只會在5秒的期間內收到一位有風險偵測到電子郵件的使用者。 這表示如果有多個使用者在相同的5秒期間內移至指定的風險層級，我們將會匯總並傳送一封電子郵件，代表所有人的風險層級變更。

![「偵測到具有風險的使用者」電子郵件](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>設定具有風險偵測到警示的使用者

如果您是系統管理員，您可以設定：

- **觸發此電子郵件產生的使用者風險層**級-根據預設，風險層級會設定為「高」風險。
- **此電子郵件的收件者** - 收件者預設包含所有「全域管理員」。 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。
   - （選擇性）您可以**新增其他電子郵件來接收警示通知**這項功能為預覽狀態，且定義的使用者必須具有適當的許可權，才能在 Azure 入口網站中查看連結的報表。

在 [有風險的使用者偵測**Azure portal** **Azure Active Directory**到警示] Azure Active Directory 的 [  >  **安全性**身分  >  **識別保護**  >  **使用者**] 底下的 Azure 入口網站中，設定風險電子郵件。

## <a name="weekly-digest-email"></a>每週精選文章電子郵件

每週摘要電子郵件包含新風險偵測的摘要。  
其中包括：

- 偵測到新的具風險使用者
- 偵測到新的具風險登入（即時）
- Identity Protection 中相關報告的連結

![每週精選文章電子郵件](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

根據預設，收件者包含所有全域管理員。 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。

### <a name="configure-weekly-digest-email"></a>設定每週摘要電子郵件

身為系統管理員，您可以開啟或關閉傳送每週摘要電子郵件，然後選擇指派要接收電子郵件的使用者。

在 [ **Azure portal** **Azure Active Directory**  >  **安全性**  >  **識別保護**] [  >  **每週摘要**] 底下的 [Azure 入口網站中，設定每週摘要電子郵件。

## <a name="see-also"></a>另請參閱

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
