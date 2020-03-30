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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120121"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知

Azure AD 標識保護髮送兩種類型的自動通知電子郵件，以説明您管理使用者風險和風險檢測：

- 「偵測到具有風險的使用者」電子郵件
- 每週精選文章電子郵件

本文同時提供您這兩種通知電子郵件的概觀。

## <a name="users-at-risk-detected-email"></a>「偵測到具有風險的使用者」電子郵件

當偵測到具有風險的帳戶時，Azure AD Identity Protection 會產生主旨為**偵測到具有風險的使用者**的電子郵件警示來作為回應。 此電子郵件會包含**[標幟為有風險的使用者](../reports-monitoring/concept-user-at-risk.md)** 報告的連結。 最佳做法是應立即調查具有風險的使用者。

此警報的配置允許您指定要生成警報的使用者風險級別。 當使用者的風險級別達到您指定的水準時，將生成電子郵件;但是，在使用者移動到此使用者風險級別後，您不會收到檢測到此使用者的電子郵件警報的新使用者。 例如，如果將策略設置為提醒中等使用者風險，而您的使用者 John 移動到中等風險，您將收到 John 檢測到的風險使用者電子郵件。 但是，如果 John 然後移動到高風險或具有其他風險檢測功能，您將不會收到第二個風險檢測警報使用者。

![「偵測到具有風險的使用者」電子郵件](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>配置風險檢測警報的使用者

如果您是系統管理員，您可以設定：

- **觸發生成此電子郵件的使用者風險級別**- 預設情況下，風險級別設置為"高"風險。
- **此電子郵件的收件者** - 收件者預設包含所有「全域管理員」。 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。
   - 可以選擇**添加其他電子郵件以接收警報通知**此功能是預覽，定義的使用者必須具有在 Azure 門戶中查看連結報表的適當許可權。

在**Azure 活動目錄** > **安全** > **標識保護** > 下在**Azure 門戶**中配置有風險電子郵件**的使用者，這些使用者處於風險檢測警報中**。

## <a name="weekly-digest-email"></a>每週精選文章電子郵件

每週摘要電子郵件包含新風險檢測摘要。  
其中包括：

- 有風險的使用者
- 可疑的活動
- 偵測到的弱點
- Identity Protection 中相關報告的連結

![每週精選文章電子郵件](./media/howto-identity-protection-configure-notifications/400.png)

預設情況下，收件者包括所有全域管理員。 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。

### <a name="configure-weekly-digest-email"></a>配置每週摘要電子郵件

作為管理員，您可以切換每週發送摘要電子郵件，並選擇分配接收電子郵件的使用者。

在**Azure 活動目錄** > **安全** > **標識保護** > **周摘要**下在**Azure 門戶**中配置每週摘要電子郵件。

## <a name="see-also"></a>另請參閱

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
