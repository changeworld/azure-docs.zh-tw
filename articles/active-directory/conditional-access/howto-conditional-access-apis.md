---
title: 條件式存取 Api 和 PowerShell-Azure Active Directory
description: 使用 Azure AD 條件式存取 Api 和 PowerShell 來管理程式碼之類的原則
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4dd6e64053af41d66e3accbb7fc9b21d951fc6
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860208"
---
# <a name="conditional-access-programmatic-access"></a>條件式存取：以程式設計方式存取

許多組織都表示他們需要盡可能地管理大部分的環境，例如程式碼。 您可以使用 Microsoft Graph 來處理條件式存取原則，例如環境中的任何其他程式碼。

Microsoft Graph 提供統一的可程式性模型，可讓組織用來與 Microsoft 365、Windows 10 和 Enterprise Mobility + Security 中的資料互動。 如需 Microsoft Graph 的詳細資訊，請參閱 [Microsoft Graph 的總覽](/graph/overview)。

![顯示屬於圖形一部分之主要資源和關聯性的影像](./media/howto-conditional-access-apis/microsoft-graph.png)

下列範例是以不支援的形式提供。 您可以使用這些範例作為組織中的工具基礎。 

下列許多範例都會使用 [受控](../managed-identities-azure-resources/overview.md)識別、 [Logic Apps](../../logic-apps/logic-apps-overview.md)、 [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage)、 [小組](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)和 [Azure Key Vault](../../key-vault/general/overview.md)等工具。

## <a name="configure"></a>設定

### <a name="powershell"></a>PowerShell

對於許多系統管理員而言，PowerShell 已經是可理解的腳本工具。 下列範例顯示如何使用 [Azure AD PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD) 來管理條件式存取原則。

- [使用 Azure AD PowerShell 命令來設定條件式存取原則](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>圖形 API

此範例顯示條件式存取圖形 Api 中可用的基本建立、讀取、更新和刪除 (CRUD) 選項。 此範例也包含一些 JSON 範本，您可以使用這些範本來建立一些範例原則。

- [使用 Microsoft Graph API 呼叫來設定條件式存取原則](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>使用範本設定

使用條件式存取 Api，在生產前環境中使用範本部署條件式存取原則。

- [使用 Microsoft Graph API 範本來設定條件式存取原則](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>測試

此範例會使用核准工作流程，將條件式存取原則從一個環境（例如生產環境）複製到另一個環境，例如生產環境。

- [從測試環境升級條件式存取原則](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>部署

此範例提供一種機制，可讓您在使用者擴展中逐漸執行分段部署條件式存取原則，讓您可以管理支援影響並及早找出問題。

- [使用核准工作流程將條件式存取原則部署到生產環境](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>監視器

此範例提供一種機制來監視一段時間的條件式存取原則變更，並且可在變更金鑰原則時觸發警示。

- [監視已部署的條件式存取原則，以進行變更和觸發警示](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>管理

### <a name="backup-and-restore"></a>備份與還原

使用此範例將條件式存取原則的備份和還原，與小組中的核准自動化。

- [使用 Microsoft Graph API 呼叫來管理條件式存取原則的備份和還原流程](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>緊急存取帳戶

多個系統管理員可以建立條件式存取原則，而且可能會忘記將 [緊急存取帳戶](../roles/security-emergency-access.md) 新增為這些原則的排除。 此範例會確保所有原則都會更新，以包含您指定的緊急存取帳戶。

- [使用 Microsoft Graph API 呼叫，管理將緊急存取帳戶指派給條件式存取原則](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>應變計劃

這種情況不一定會以您想要的方式運作，當發生這種情況時，您需要一種方法來回到工作可繼續的狀態。 下列範例提供一種方法，讓您將原則還原為已知的良好應變計劃，並停用其他條件式存取原則。

- [使用 Microsoft Graph API 呼叫來管理條件式存取應變原則的啟用](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>社區投稿

這些範例可在我們的 [GitHub 存放庫](https://github.com/Azure-Samples/azure-ad-conditional-access-apis)中取得。 我們很樂意支援 GitHub 問題和提取要求的社區貢獻。

## <a name="next-steps"></a>後續步驟

- [Microsoft Graph 概觀](/graph/overview)

- [條件式存取 API](/graph/api/resources/conditionalaccesspolicy)

- [命名位置 API](/graph/api/resources/namedlocation)
