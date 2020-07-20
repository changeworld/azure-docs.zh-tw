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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77185917"
---
# <a name="conditional-access-classic-policy-migration"></a>條件式存取傳統原則遷移

條件式存取是 Azure Active Directory 用來將訊號結合在一起、進行決策及強制執行組織原則的工具。 條件式存取如何成為新身分識別導向控制平面的核心。 雖然目的仍然相同，但新 Azure 入口網站的發行已引進條件式存取如何運作的重大改善。

請考慮移轉尚未在 Azure 入口網站中建立的原則，因為：

- 您現在可以處理之前無法處理的案例。
- 您可以合併它們以減少必須管理的原則數目。
- 您可以在一個集中位置管理所有條件式存取原則。
- Azure 傳統入口網站將被淘汰。

本文說明將現有的條件式存取原則遷移至新架構時需要知道的事項。

## <a name="classic-policies"></a>傳統原則

在[Azure 入口網站](https://portal.azure.com)中，可以在**Azure Active Directory**  >  **安全性**  >  **條件式存取**底下找到條件式存取原則。 您的組織可能也會有較舊的條件式存取原則，而不是使用此頁面建立。 這些原則稱為「*傳統原則」*。 傳統原則是您在中建立的條件式存取原則：

- Azure 傳統入口網站
- Intune 傳統入口網站
- Intune 應用程式防護入口網站

在 [**條件式存取**] 頁面上，按一下 [**管理**] 區段中的 [[**傳統原則**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)]，即可存取您的傳統原則。 

![Azure AD 中的條件式存取，顯示傳統原則視圖](./media/policy-migration/71.png)

**傳統原則**檢視會提供選項以便：

- 篩選傳統原則。
- 停用傳統原則。
- 檢查傳統原則的設定，並將其停用。

   ![傳統原則詳細資料，包括現有的原則設定](./media/policy-migration/74.png)

> [!WARNING]
> 一旦停用，就無法重新啟用傳統原則。

傳統原則的詳細資料檢視可讓您記錄設定、修改包含或排除的群組，以及停用原則。

![原則詳細資料-要包含或排除的群組](./media/policy-migration/75.png)

藉由變更選取的群組或排除特定群組，您可以在停用所有包含的使用者和群組的原則之前，先測試一些測試使用者的已停用傳統原則效果。
 
## <a name="migration-considerations"></a>移轉考量

在本文中，Azure AD 條件式存取原則也稱為「*新原則*」。
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

![選取用戶端應用程式的條件式存取](./media/policy-migration/64.png)

如果傳統原則包含數個條件，也不可能彙總成一個新的原則。 以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則不支援其他條件：   

![Exchange ActiveSync 不支援選取的條件](./media/policy-migration/08.png)

如果您已設定以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則，您必須確定並未設定其他條件。 

![條件式存取條件](./media/policy-migration/16.png)
 
Office 365 Exchange Online 的應用程式型傳統原則，其中包含做為用戶端應用程式條件的**Exchange Active Sync** ，可允許**支援**和**不支援**的裝置平臺。 雖然您無法在相關的新原則中設定個別裝置平台，但您可以讓支援僅限於[支援的裝置平台](concept-conditional-access-conditions.md#device-platforms)。 

![條件式存取選取 Exchange ActiveSync](./media/policy-migration/65.png)

您可以彙總多個將 **Exchange Active Sync** 納入為用戶端應用程式條件的傳統原則，前提是您：

- 只有 **Exchange Active Sync** 作為條件 
- 已設定授與存取的幾項需求

常見的案例之一：

- 從 Azure 傳統入口網站彙總以裝置為基礎的傳統原則 
- 在 Intune 應用程式防護入口網站中彙總以應用程式為基礎的傳統原則 
 
在此情況下，您可以將傳統原則彙總成一個已選取兩項需求的新原則。

![條件式存取授與控制項](./media/policy-migration/62.png)

### <a name="device-platforms"></a>裝置平台

具有以應用程式為基礎的控制項的傳統原則會將 iOS 和 Android 預先設定為裝置平台條件。 

在新的原則中，您必須選取想要個別支援的[裝置平台](concept-conditional-access-conditions.md#device-platforms)。

![條件式存取裝置平臺選擇](./media/policy-migration/41.png)

## <a name="next-steps"></a>後續步驟

- [使用僅限報告模式來進行條件式存取，以判斷新原則決策的影響。](concept-conditional-access-report-only.md)
- 如果您想要知道如何設定條件式存取原則，請參閱[條件式存取的一般原則](concept-conditional-access-policy-common.md)。
- 如果您已準備好設定環境的條件式存取原則，請參閱[如何：在 Azure Active Directory 中規劃條件式存取部署](plan-conditional-access.md)一文。 
