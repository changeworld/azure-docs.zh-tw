---
title: Azure Active Directory Identity Protection 通知
description: 了解通知如何支援您的調查活動。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77120121"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知

Azure AD Identity Protection 會傳送兩種類型的自動化通知電子郵件，協助您管理使用者風險和風險偵測：

- 「偵測到具有風險的使用者」電子郵件
- 每週精選文章電子郵件

本文同時提供您這兩種通知電子郵件的概觀。

## <a name="users-at-risk-detected-email"></a>「偵測到具有風險的使用者」電子郵件

當偵測到具有風險的帳戶時，Azure AD Identity Protection 會產生主旨為**偵測到具有風險的使用者**的電子郵件警示來作為回應。 此電子郵件會包含**[標幟為有風險的使用者](../reports-monitoring/concept-user-at-risk.md)** 報告的連結。 最佳做法是應立即調查具有風險的使用者。

此警示的設定可讓您指定要產生警示的使用者風險層級。 當使用者的風險層級達到您指定的值時，就會產生電子郵件;不過，在使用者移至此使用者風險層級之後，您將不會收到有風險的新使用者偵測到此使用者的電子郵件警示。 例如，如果您將原則設定為針對中型使用者風險發出警示，而您的使用者 John 移至中等風險，您會收到 John 偵測到有風險的使用者電子郵件。 不過，如果 John 接著進入高風險或有額外的風險偵測，您就不會收到第二位有風險的使用者偵測到警示。

![「偵測到具有風險的使用者」電子郵件](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>設定具有風險偵測到警示的使用者

如果您是系統管理員，您可以設定：

- **觸發此電子郵件產生的使用者風險層**級-根據預設，風險層級會設定為「高」風險。
- **此電子郵件的收件者** - 收件者預設包含所有「全域管理員」。 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。
   - （選擇性）您可以**新增其他電子郵件來接收警示通知**這項功能為預覽狀態，且定義的使用者必須具有適當的許可權，才能在 Azure 入口網站中查看連結的報表。

在 [有風險的使用者偵測**到警示**] **Azure Active Directory** > 的 [**安全性** > 身分**識別保護** > 使用者] 底下的**Azure 入口網站**中，設定風險電子郵件。

## <a name="weekly-digest-email"></a>每週精選文章電子郵件

每週摘要電子郵件包含新風險偵測的摘要。  
其中包括：

- 有風險的使用者
- 可疑的活動
- 偵測到的弱點
- Identity Protection 中相關報告的連結

![每週精選文章電子郵件](./media/howto-identity-protection-configure-notifications/400.png)

根據預設，收件者包含所有全域管理員。 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。

### <a name="configure-weekly-digest-email"></a>設定每週摘要電子郵件

身為系統管理員，您可以開啟或關閉傳送每週摘要電子郵件，然後選擇指派要接收電子郵件的使用者。

在 [ **Azure Active Directory** > **安全性** > **識別保護** > ] [**每週摘要**] 底下的 [ **Azure 入口網站**中，設定每週摘要電子郵件。

## <a name="see-also"></a>請參閱

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
