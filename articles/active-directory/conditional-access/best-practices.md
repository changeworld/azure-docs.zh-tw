---
title: Azure Active Directory 中條件式存取的最佳做法 |Microsoft Docs
description: 瞭解您應該知道的事項，以及您在設定條件式存取原則時應避免執行的動作。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 161b02ec962df5c5e1c8dee7e124ef78b3ca4db3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601974"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中條件式存取的最佳作法

使用 [Azure Active Directory (Azure AD) 條件式存取](./overview.md)時，您可以控制授權使用者如何存取您的雲端應用程式。 本文提供以下相關資訊：

- 您應該知道的事情 
- 在設定條件式存取原則時，您應該避免這樣做。 

本文假設您已熟悉[Azure Active Directory 中條件式存取](./overview.md)所述的概念和術語？

## <a name="whats-required-to-make-a-policy-work"></a>讓原則運作的必要條件為何？

當您建立新的原則時，未選取任何使用者、群組、應用程式或存取控制項。

![雲端應用程式](./media/best-practices/02.png)

若要讓原則正常執行，您必須設定：

| 對象           | 方式                                  | 原因 |
| :--            | :--                                  | :-- |
| **雲端應用程式** |選取一或多個應用程式。  | 條件式存取原則的目標是讓您控制授權的使用者如何存取雲端應用程式。|
| **使用者和群組** | 選取至少一個已獲授權可存取所選雲端應用程式的使用者或群組。 | 永遠不會觸發未指派使用者和群組的條件式存取原則。 |
| **存取控制** | 選取至少一個存取控制。 | 如果滿足條件，則原則處理器必須知道該怎麼做。 |

## <a name="what-you-should-know"></a>您應該知道的事項

### <a name="how-are-conditional-access-policies-applied"></a>如何套用條件式存取原則？

當您存取雲端應用程式時，可能會有多個條件式存取原則適用。 在此情況下，您必須滿足所有套用的原則。 例如，如果某個原則需要 (MFA) 的多重要素驗證，而另一個原則需要符合規範的裝置，您必須完成 MFA，並使用符合規範的裝置。 

所有的原則都會在兩個階段強制執行：

- 第1階段：收集會話詳細資料 
   - 收集需要進行原則評估的會話詳細資料，例如使用者位置和裝置身分識別。 
   - 在這個階段中，如果裝置合規性是條件式存取原則的一部分，使用者可能會看到憑證提示。 當裝置作業系統不 Windows 10 時，瀏覽器應用程式可能會出現此提示。 
   - 原則評估的第1階段是在 [僅限報表模式下](concept-conditional-access-report-only.md)啟用的原則和原則進行。
- 第2階段：強制 
   - 使用階段1中收集的會話詳細資料，以找出任何未符合的需求。 
   - 如果有設定為封鎖存取權的原則，則會在此處停止強制執行，而且會封鎖使用者。 
   - 然後，系統會提示使用者完成不符合下列順序的第1階段所滿足的額外授與控制需求，直到符合原則為止：  
      - Multi-Factor Authentication 
      - 核准的用戶端應用程式/應用程式保護原則 
      - 受管理的裝置 (符合規範或混合式 Azure AD 聯結)  
      - 使用規定 
      - 自訂控制項  
      - 滿足 grant 控制項之後，請將會話控制項套用 (應用程式強制執行、Microsoft Cloud App Security 和權杖存留期)  
   - 原則評估的第2階段會針對所有啟用的原則進行。 

### <a name="how-are-assignments-evaluated"></a>如何評估指派？

所有指派邏輯上都會使用 **AND** 運算子。 如果您已設定多個指派，必須滿足所有的指派才能觸發原則。  

如果您需要設定一個位置條件，以套用至從您的組織網路外部進行的所有連線︰

- 包含**所有位置**
- 排除**所有信任的 IP**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>如果被鎖定在 Azure AD 管理入口網站之外該怎麼辦？

如果您因為條件式存取原則中的設定不正確，而遭到鎖定而無法使用 Azure AD 入口網站：

- 確認您的組織中是否有其他系統管理員尚未遭到封鎖。 具有 Azure 入口網站存取權的系統管理員可以停用影響您登入的原則。 
- 如果您的組織中沒有任何系統管理員可以更新原則，您需要提交支援要求。 Microsoft 支援服務可以檢查並更新阻礙存取的條件式存取原則。

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果您已在 Azure 傳統入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？  

Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果您已在 Intune Silverlight 入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？

Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果我已針對同一個使用者設定多個原則，則會發生什麼情況？  

針對每次登入，Azure Active Directory 會評估所有的原則，並確保在授與使用者存取權之前已符合所有的需求。 封鎖存取的優先順序高過所有其他組態設定。 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>條件式存取可搭配 Exchange ActiveSync 使用嗎？

是，您可以在條件式存取原則中使用 Exchange ActiveSync。

某些雲端應用程式（例如 SharePoint 和 Exchange Online）也支援舊版驗證通訊協定。 當用戶端應用程式可以使用舊版驗證通訊協定來存取雲端應用程式時，Azure AD 無法在此存取嘗試上強制執行條件式存取原則。 若要預防用戶端應用程式略過強制執行的原則，您應該確認是否可以只針對受影響的雲端應用程式啟用新式驗證。

### <a name="how-should-you-configure-conditional-access-with-microsoft-365-apps"></a>如何使用 Microsoft 365 apps 來設定條件式存取？

由於 Microsoft 365 apps 互相互連，因此我們建議您在建立原則時，同時指派常用的應用程式。

常見的互連應用程式包括 Microsoft Flow、Microsoft Planner、Microsoft 小組、Exchange Online、SharePoint 和 Yammer。

當您在會話或工作的開頭控制存取權時，需要使用者互動的原則（例如多重要素驗證）很重要。 如果您沒有這麼做，使用者將無法在應用程式中完成一些工作。 例如，如果您在未受管理的裝置上需要多重要素驗證以存取 SharePoint，但不需要傳送電子郵件，則在其電子郵件中工作的使用者將無法將 SharePoint 檔案附加到訊息。 如需詳細資訊，請參閱文章中的 [Azure Active Directory 條件式存取中的服務相依性為何？](service-dependencies.md)。

## <a name="what-you-should-avoid-doing"></a>您應該避免做的事

條件式存取架構可為您提供絕佳的設定彈性。 不過，絕佳的彈性也意謂著您應該先仔細地檢閱每個設定原則，然後才發行它，以避免產生不想要的結果。 在此情況下，您應該特別注意影響整個集合的指派，例如 **all users / groups / cloud apps**。

在您的環境中，應該避免使用下列組態：

**針對所有使用者、所有雲端應用程式：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。
- **需要符合規範的裝置** - 針對尚未註冊其裝置的使用者，此原則會封鎖所有存取，包括對 Intune 入口網站的存取。 如果您是沒有已註冊裝置的系統管理員，此原則會阻擋您回到 Azure 入口網站來變更此原則。
- **需要加入網域** - 如果您還沒有已加入網域的裝置，此原則也可能封鎖您組織中所有使用者的存取。
- **需要應用程式保護原則** -如果您沒有 Intune 原則，此原則封鎖存取也有可能封鎖您組織中所有使用者的存取權。 如果您的系統管理員沒有具有 Intune 應用程式保護原則的用戶端應用程式，此原則會封鎖您回到 Intune 和 Azure 等入口網站。

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
- 您可以在單一中央位置管理所有條件式存取原則。
- Azure 傳統入口網站將被淘汰。   

如需詳細資訊，請參閱[在 Azure 入口網站中移轉傳統原則](policy-migration.md)。

## <a name="next-steps"></a>後續步驟

如果您想要知道：

- 如何設定條件式存取原則，請參閱 [使用 Azure Active Directory 條件式存取的特定應用程式需要 MFA](../authentication/tutorial-enable-azure-mfa.md)。
- 如何規劃條件式存取原則，請參閱 [如何在 Azure Active Directory 中規劃條件式存取部署](plan-conditional-access.md)。