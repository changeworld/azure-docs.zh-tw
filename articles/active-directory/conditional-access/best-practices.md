---
title: Azure 活動目錄中的條件訪問最佳做法 |微軟文檔
description: 瞭解在配置條件訪問策略時應瞭解的內容以及應避免執行的操作。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295349"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure 活動目錄中的條件訪問最佳做法

使用[Azure 活動目錄 （Azure AD） 條件訪問](../active-directory-conditional-access-azure-portal.md)，可以控制授權使用者訪問雲應用的方式。 本文提供以下相關資訊：

- 您應該知道的事情 
- 在配置條件訪問策略時，應避免執行哪些內容。 

本文假定您熟悉[Azure 活動目錄中的條件訪問中](../active-directory-conditional-access-azure-portal.md)概述的概念和術語？

## <a name="whats-required-to-make-a-policy-work"></a>使策略發揮作用需要什麼？

當您建立新的原則時，未選取任何使用者、群組、應用程式或存取控制項。

![雲端應用程式](./media/best-practices/02.png)

若要讓您的原則運作，您必須設定：

| 何事           | 方式                                  | 理由 |
| :--            | :--                                  | :-- |
| **雲應用** |選取一或多個應用程式。  | 條件訪問策略的目標是使您能夠控制授權使用者訪問雲應用的方式。|
| **使用者和組** | 選取至少一個已獲授權存取您所選雲端應用程式的使用者或群組。 | 永遠不會觸發沒有分配使用者和組的條件訪問策略。 |
| **存取控制** | 選取至少一個存取控制。 | 如果滿足您的條件，您的原則處理器需要知道該怎麼辦。 |

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="how-are-conditional-access-policies-applied"></a>如何應用條件訪問策略？

當您訪問雲應用時，可能會應用多個條件訪問策略。 在此情況下，您必須滿足所有套用的原則。 例如，如果一個策略需要多重要素驗證 （MFA），而另一個策略需要符合的設備，則必須完成 MFA 並使用相容的設備。 

所有的原則都會在兩個階段強制執行：

- 第 1 階段： 
   - 詳細資訊集合：收集詳細資訊以確定已滿足的策略。
   - 在此階段，如果設備合規性是條件訪問策略的一部分，使用者可能會看到證書提示。 當設備作業系統不是 Windows 10 時，瀏覽器應用可能會出現此提示。
   - 策略評估的第一階段發生在[僅報告模式下](concept-conditional-access-report-only.md)的所有啟用的策略和策略。
- 第 2 階段：
   - 實施：考慮到第 1 階段收集的詳細資訊，請使用者滿足尚未滿足的任何其他要求。
   - 將結果應用於會話。 
   - 策略評估的第 2 階段適用于所有啟用的策略。

### <a name="how-are-assignments-evaluated"></a>如何評估指派？

所有指派邏輯上都會使用 **AND** 運算子。 如果您已設定多個指派，必須滿足所有的指派才能觸發原則。  

如果您需要設定一個位置條件，以套用至從您的組織網路外部進行的所有連線︰

- 包含**所有位置**
- 排除**所有信任的 IP**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>如果被鎖定在 Azure AD 管理入口網站之外該怎麼辦？

如果由於條件訪問策略中的設置不正確而被鎖定在 Azure AD 門戶之外：

- 確認您的組織中是否有其他系統管理員尚未遭到封鎖。 具有 Azure 入口網站存取權的系統管理員可以停用會影響您的登入的原則。 
- 如果您的組織中沒有任何系統管理員可以更新原則，您需要提交支援要求。 Microsoft 支援可以查看和更新阻止訪問的條件訪問策略。

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果您已在 Azure 傳統入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？  

Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果您已在 Intune Silverlight 入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？

Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果我已針對同一個使用者設定多個原則，則會發生什麼情況？  

針對每次登入，Azure Active Directory 會評估所有的原則，並確保在授與使用者存取權之前已符合所有的需求。 封鎖存取的優先順序高過所有其他組態設定。 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>條件訪問是否與 Exchange 活動同步配合使用？

可以，您可以在條件訪問策略中使用 Exchange ActiveSync。

某些雲應用（如 SharePoint Online 和 Exchange Online）也支援舊版身份驗證協定。 當用戶端應用可以使用舊版身份驗證協定訪問雲應用時，Azure AD 無法在此訪問嘗試上強制實施條件訪問策略。 若要預防用戶端應用程式略過強制執行的原則，您應該確認是否可以只針對受影響的雲端應用程式啟用新式驗證。

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>如何使用 Office 365 應用配置條件訪問？

由於 Office 365 應用是互連的，因此我們建議在創建策略時將常用應用分配在一起。

常見的互連應用程式包括微軟流、微軟規劃器、微軟團隊、Office 365 線上交換、Office 365 SharePoint 線上和 Office 365 Yammer。

當訪問在會話或任務開始時受到控制時，對於需要使用者交互的策略（如多重要素驗證）非常重要。 如果沒有，使用者將無法在應用中完成某些任務。 例如，如果您在非託管設備上需要多重要素驗證才能訪問 SharePoint，但不需要發送電子郵件，則在其電子郵件中工作的使用者將無法將 SharePoint 檔附加到郵件。 有關詳細資訊，請參閱["Azure 活動目錄條件訪問中的什麼是服務依賴項？"。](service-dependencies.md)

## <a name="what-you-should-avoid-doing"></a>您應該避免做的事

條件訪問框架為您提供了極大的配置靈活性。 不過，絕佳的彈性也意謂著您應該先仔細地檢閱每個設定原則，然後才發行它，以避免產生不想要的結果。 在此情況下，您應該特別注意影響整個集合的指派，例如 **all users / groups / cloud apps**。

在您的環境中，應該避免使用下列組態：

**針對所有使用者、所有雲端應用程式：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。
- **需要符合規範的裝置** - 針對尚未註冊其裝置的使用者，此原則會封鎖所有存取，包括對 Intune 入口網站的存取。 如果您是沒有已註冊裝置的系統管理員，此原則會阻擋您回到 Azure 入口網站來變更此原則。
- **需要加入網域** - 如果您還沒有已加入網域的裝置，此原則也可能封鎖您組織中所有使用者的存取。
- **需要應用保護原則**- 如果沒有 Intune 策略，此策略阻止訪問還可能阻止組織中所有使用者的訪問。 如果您是沒有具有 Intune 應用保護原則的用戶端應用程式的管理員，則此策略會阻止您返回 Intune 和 Azure 等門戶。

**針對所有使用者、所有雲端應用程式、所有裝置平台：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。

## <a name="how-should-you-deploy-a-new-policy"></a>您應該如何部署新的原則？

第一步，您應該使用[該怎麼辦工具](what-if-tool.md)評估您的原則。

為您的環境準備好新原則時，請分階段部署它們：

1. 將原則套用至較少的使用者，確認它如預期般運作。 
1. 當您展開原則，以納入更多使用者時。 繼續排除原則中的所有系統管理員，以確保他們仍然擁有存取權，而且可以在需要變更時，更新原則。
1. 必要時，將原則套用到所有使用者。 

最佳做法是建立這一個使用者帳戶：

- 專門用來管理原則 
- 從您的原則中排除

## <a name="policy-migration"></a>原則移轉

請考慮移轉尚未在 Azure 入口網站中建立的原則，因為：

- 您現在可以處理之前無法處理的案例。
- 您可以合併它們以減少必須管理的原則數目。   
- 您可以在一個中心位置管理所有條件訪問策略。
- Azure 傳統入口網站將被淘汰。   

如需詳細資訊，請參閱[在 Azure 入口網站中移轉傳統原則](policy-migration.md)。

## <a name="next-steps"></a>後續步驟

如果您想要知道：

- 如何配置條件訪問策略，請參閱使用[Azure 活動目錄條件訪問的特定應用需要 MFA。](app-based-mfa.md)
- 如何規劃條件訪問策略，請參閱如何在[Azure 活動目錄中規劃條件訪問部署](plan-conditional-access.md)。
