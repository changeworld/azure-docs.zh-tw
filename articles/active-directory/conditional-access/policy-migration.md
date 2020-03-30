---
title: 遷移條件式存取原則 - Azure Active Directory
description: 了解如何在 Azure 入口網站中移轉傳統原則中。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185917"
---
# <a name="conditional-access-classic-policy-migration"></a>條件訪問經典策略遷移

條件式存取是 Azure Active Directory 用來將訊號結合在一起、進行決策及強制執行組織原則的工具。 條件式存取如何成為新身分識別導向控制平面的核心。 雖然目的仍然相同，但新的 Azure 門戶的發佈對條件訪問的工作方式進行了重大改進。

請考慮移轉尚未在 Azure 入口網站中建立的原則，因為：

- 您現在可以處理之前無法處理的案例。
- 您可以合併它們以減少必須管理的原則數目。
- 您可以在一個中心位置管理所有條件訪問策略。
- Azure 傳統入口網站將被淘汰。

本文介紹了將現有條件訪問策略遷移到新框架需要瞭解的內容。

## <a name="classic-policies"></a>傳統原則

在[Azure 門戶](https://portal.azure.com)中，可以在**Azure 活動目錄** > **安全** > **條件訪問**下找到條件訪問策略。 您的組織也可能沒有使用此頁面創建較舊的條件訪問策略。 這些原則也稱為「傳統原則」**。 經典策略是條件訪問策略，您在中創建：

- Azure 傳統入口網站
- Intune 傳統入口網站
- Intune 應用程式防護入口網站

在 **"條件訪問"** 頁上，您可以通過按一下 **"管理**"部分[**中的經典策略**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)來訪問經典策略。 

![Azure AD 中顯示經典策略視圖的條件訪問](./media/policy-migration/71.png)

**傳統原則**檢視會提供選項以便：

- 篩選傳統原則。
- 停用傳統原則。
- 查看經典策略的設置並禁用它。

   ![經典策略詳細資訊，包括現有策略配置](./media/policy-migration/74.png)

> [!WARNING]
> 禁用後，無法重新啟用經典策略。

經典策略的詳細資訊視圖允許您記錄設置、修改包含或排除的組以及禁用策略。

![策略詳細資訊 - 要包括或排除的組](./media/policy-migration/75.png)

通過更改所選組或排除特定組，可以在禁用所有包含的使用者和組的策略之前為少數測試使用者測試禁用經典策略的效果。
 
## <a name="migration-considerations"></a>移轉考量

在本文中，Azure AD 條件訪問策略也稱為*新策略*。
您的傳統原則會繼續與新原則並存運作，直到您停用或刪除它們為止。 

下列是原則彙總內容中的重要層面：

- 雖然傳統原則會繫結至特定雲端應用程式，但您可以在一個新原則中選取您所需要數量的雲端應用程式。
- 雲端應用程式的傳統原則與新原則的控制項要求滿足所有的控制項 (*AND*)。 
- 在新原則中，您可以：
   - 結合多個條件 (如果您的案例所需)。 
   - 選取數個授與需求作為存取控制，並將它們與邏輯 *OR* (需要其中一個選取的控制項) 或與邏輯*AND* (需要所有選取的控制項)結合。

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

如果您想要移轉的 **Office 365 Exchange Online** 傳統原則將 **Exchange Active Sync** 納入為用戶端應用程式條件，您可能無法將其彙總成一個新原則。 

例如，如果您想要支援所有的用戶端應用程式類型，就是這種情況。 在以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則中，您無法選取其他用戶端應用程式。

![條件訪問選擇用戶端應用](./media/policy-migration/64.png)

如果傳統原則包含數個條件，也不可能彙總成一個新的原則。 以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則不支援其他條件：   

![Exchange ActiveSync 不支援所選條件](./media/policy-migration/08.png)

如果您已設定以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則，您必須確定並未設定其他條件。 

![條件訪問條件](./media/policy-migration/16.png)
 
基於應用的 Office 365 交換線上策略，包括作為用戶端應用條件**交換活動同步**，允許**支援**和**不支援**的設備平臺。 雖然您無法在相關的新原則中設定個別裝置平台，但您可以讓支援僅限於[支援的裝置平台](concept-conditional-access-conditions.md#device-platforms)。 

![條件訪問選擇交換活動同步](./media/policy-migration/65.png)

您可以彙總多個將 **Exchange Active Sync** 納入為用戶端應用程式條件的傳統原則，前提是您：

- 只有 **Exchange Active Sync** 作為條件 
- 已設定授與存取的幾項需求

常見的案例之一：

- 從 Azure 傳統入口網站彙總以裝置為基礎的傳統原則 
- 在 Intune 應用程式防護入口網站中彙總以應用程式為基礎的傳統原則 
 
在此情況下，您可以將傳統原則彙總成一個已選取兩項需求的新原則。

![條件訪問授予控制項](./media/policy-migration/62.png)

### <a name="device-platforms"></a>裝置平台

具有以應用程式為基礎的控制項的傳統原則會將 iOS 和 Android 預先設定為裝置平台條件。 

在新的原則中，您必須選取想要個別支援的[裝置平台](concept-conditional-access-conditions.md#device-platforms)。

![條件訪問設備平臺選擇](./media/policy-migration/41.png)

## <a name="next-steps"></a>後續步驟

- [對條件訪問使用僅報告模式來確定新策略決策的影響。](concept-conditional-access-report-only.md)
- 如果想知道如何配置條件訪問策略，請參閱[條件訪問常見策略](concept-conditional-access-policy-common.md)。
- 如果已準備好為環境配置條件訪問策略，請參閱"[如何：在 Azure 活動目錄中規劃條件訪問部署](plan-conditional-access.md)"一文。 
