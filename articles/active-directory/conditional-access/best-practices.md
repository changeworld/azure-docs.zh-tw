---
title: Azure Active Directory 中的條件式存取最佳做法 |Microsoft Docs
description: 瞭解您在設定條件式存取原則時應該知道的事項，以及應避免進行的動作。
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
ms.openlocfilehash: ccfbb31c29b9e240a4865c8d7d98d7b6af00d1fd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377566"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中的條件式存取最佳做法

透過[Azure Active Directory （Azure AD）條件式存取](../active-directory-conditional-access-azure-portal.md)，您可以控制授權使用者如何存取您的雲端應用程式。 本文提供以下相關資訊：

- 您應該知道的事情 
- 當您設定條件式存取原則時，應避免執行此動作。 

本文假設您已熟悉[Azure Active Directory 的條件式存取中](../active-directory-conditional-access-azure-portal.md)所述的概念和詞彙？

## <a name="whats-required-to-make-a-policy-work"></a>讓原則運作的必要條件？

當您建立新的原則時，未選取任何使用者、群組、應用程式或存取控制項。

![雲端應用程式](./media/best-practices/02.png)

若要讓您的原則運作，您必須設定：

| 何事           | 方式                                  | 理由 |
| :--            | :--                                  | :-- |
| **雲端應用程式** |選取一或多個應用程式。  | 條件式存取原則的目標是要讓您控制授權使用者可以存取雲端應用程式的方式。|
| **使用者和群組** | 選取至少一個已獲授權存取您所選雲端應用程式的使用者或群組。 | 不會觸發未指派使用者和群組的條件式存取原則。 |
| **存取控制** | 選取至少一個存取控制。 | 如果滿足您的條件，您的原則處理器需要知道該怎麼辦。 |

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="how-are-conditional-access-policies-applied"></a>條件式存取原則的套用方式為何？

當您存取雲端應用程式時，可能會有一個以上的條件式存取原則適用。 在此情況下，您必須滿足所有套用的原則。 例如，如果某個原則需要多重要素驗證（MFA），而另一個原則需要相容的裝置，您必須完成 MFA，並使用符合規範的裝置。 

所有的原則都會在兩個階段強制執行：

- 階段 1： 
   - 詳細資料集合：收集詳細資料以識別已滿足的原則。
   - 在此階段中，如果裝置合規性是條件式存取原則的一部分，使用者可能會看到憑證提示。 當裝置作業系統不是 Windows 10 時，瀏覽器應用程式可能會出現此提示。
   - 原則評估的第1階段會針對所有已啟用的原則和原則，在[僅限報表模式](concept-conditional-access-report-only.md)中進行。
- 第 2 階段：
   - 強制：考慮在階段1中收集的詳細資料，要求使用者滿足尚未符合的任何其他需求。
   - 將結果套用至會話。 
   - 原則評估的第2階段會針對所有已啟用的原則進行。

### <a name="how-are-assignments-evaluated"></a>如何評估指派？

所有指派邏輯上都會使用 **AND** 運算子。 如果您已設定多個指派，必須滿足所有的指派才能觸發原則。  

如果您需要設定一個位置條件，以套用至從您的組織網路外部進行的所有連線︰

- 包含**所有位置**
- 排除**所有信任的 IP**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>如果被鎖定在 Azure AD 管理入口網站之外該怎麼辦？

如果您因為條件式存取原則中的設定不正確而鎖定了 Azure AD 入口網站：

- 確認您的組織中是否有其他系統管理員尚未遭到封鎖。 具有 Azure 入口網站存取權的系統管理員可以停用會影響您的登入的原則。 
- 如果您的組織中沒有任何系統管理員可以更新原則，您需要提交支援要求。 Microsoft 支援服務可以審查和更新防止存取的條件式存取原則。

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果您已在 Azure 傳統入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？  

Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果您已在 Intune Silverlight 入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？

Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果我已針對同一個使用者設定多個原則，則會發生什麼情況？  

針對每次登入，Azure Active Directory 會評估所有的原則，並確保在授與使用者存取權之前已符合所有的需求。 封鎖存取的優先順序高過所有其他組態設定。 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>條件式存取是否能與 Exchange ActiveSync 搭配使用？

是，您可以在條件式存取原則中使用 Exchange ActiveSync。

某些雲端應用程式（例如 SharePoint Online 和 Exchange Online）也支援舊版驗證通訊協定。 當用戶端應用程式可以使用舊版驗證通訊協定來存取雲端應用程式時，Azure AD 無法在此存取嘗試上強制執行條件式存取原則。 若要預防用戶端應用程式略過強制執行的原則，您應該確認是否可以只針對受影響的雲端應用程式啟用新式驗證。

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>您應該如何使用 Office 365 應用程式來設定條件式存取？

由於 Office 365 應用程式是互連的，因此建議您在建立原則時，同時指派常用的應用程式。

常見的互連應用程式包括 Microsoft Flow、Microsoft Planner、Microsoft 小組、Office 365 Exchange Online、Office 365 SharePoint Online 和 Office 365 Yammer。

在會話或工作的開頭控制存取時，需要使用者互動的原則（例如多重要素驗證）很重要。 如果您沒有這麼做，使用者將無法完成應用程式內的某些工作。 例如，如果您在未受管理的裝置上要求多重要素驗證來存取 SharePoint，而不是透過電子郵件，則在其電子郵件中工作的使用者將無法將 SharePoint 檔案附加至訊息。 如需詳細資訊，請參閱這篇文章： [Azure Active Directory 條件式存取中的服務相依性為何？](service-dependencies.md)。

## <a name="what-you-should-avoid-doing"></a>您應該避免做的事

條件式存取架構為您提供絕佳的設定彈性。 不過，絕佳的彈性也意謂著您應該先仔細地檢閱每個設定原則，然後才發行它，以避免產生不想要的結果。 在此情況下，您應該特別注意影響整個集合的指派，例如 **all users / groups / cloud apps**。

在您的環境中，應該避免使用下列組態：

**針對所有使用者、所有雲端應用程式：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。
- **需要符合規範的裝置** - 針對尚未註冊其裝置的使用者，此原則會封鎖所有存取，包括對 Intune 入口網站的存取。 如果您是沒有已註冊裝置的系統管理員，此原則會阻擋您回到 Azure 入口網站來變更此原則。
- **需要加入網域** - 如果您還沒有已加入網域的裝置，此原則也可能封鎖您組織中所有使用者的存取。
- **需要應用程式保護原則**-如果您沒有 Intune 原則，則此原則封鎖存取也可能封鎖組織中所有使用者的存取。 如果您是沒有 Intune 應用程式保護原則的用戶端應用程式的系統管理員，則此原則會阻止您回到 Intune 和 Azure 等入口網站。

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
- 您可以在一個集中位置管理所有條件式存取原則。
- Azure 傳統入口網站將被淘汰。   

如需詳細資訊，請參閱[在 Azure 入口網站中移轉傳統原則](policy-migration.md)。

## <a name="next-steps"></a>後續步驟

如果您想要知道：

- 如何設定條件式存取原則的詳細說明，請參閱[使用 Azure Active Directory 條件式存取的特定應用程式需要 MFA](app-based-mfa.md)。
- 如何規劃您的條件式存取原則，請參閱[如何在 Azure Active Directory 中規劃條件式存取部署](plan-conditional-access.md)。
