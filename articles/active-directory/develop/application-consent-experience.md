---
title: Azure AD 應用程式同意體驗
titleSuffix: Microsoft identity platform
description: 深入了解 Azure AD 同意的運作方式，以了解如何在管理和開發搭配 Azure AD 執行的應用程式時使用它
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: 6e768c1e938006afd62fc097a80f8ebc3ea0f3e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88115470"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>了解 Azure AD 應用程式同意體驗

深入了解 Azure Active Directory (Azure AD) 應用程式同意的使用者體驗。 因此您能夠以明智的方式為您的組織管理應用程式，並/或開發同意體驗更順暢的應用程式。

## <a name="consent-and-permissions"></a>同意和權限

同意是使用者授權應用程式代表使用者存取受保護的資源所用的程序。 系統管理員或使用者需要同意允許存取其組織/個人資料。

實際的使用者同意體驗會根據使用者的租用戶、使用者的授權範圍 (或角色) 和用戶端應用程式要求的[權限](../azuread-dev/v1-permissions-consent.md)類型所設定的原則，而有所不同。 這表示應用程式開發人員和租用戶管理員擁有相同的同意體驗控制權。 管理員可彈性設定租用戶或應用程式控制其租用戶同意體驗的原則，和加以停用。 應用程式開發人員可以決定可要求的權限類型，以及是否要引導使用者進行使用者同意流程或管理員同意流程。

- **使用者同意流程**是應用程式開發人員將使用者導向授權端點以僅針對目前使用者記錄同意的過程。
- **管理員同意流程**是應用程式開發人員將使用者導向管理員同意端點以僅針對整個租用戶記錄同意的過程。 若要確保管理員同意流程運作正常，應用程式開發人員必須列出應用程式資訊清單中`RequiredResourceAccess`屬性的所有權限。 如需詳細資訊，請參閱[應用程式資訊清單](./reference-app-manifest.md)。

## <a name="building-blocks-of-the-consent-prompt"></a>同意提示的建置組塊

同意提示的設計可確保使用者擁有足夠的資訊來判斷是否信任用戶端應用程式，來代表自己存取受保護的資源。 了解建置組塊有助於讓使用者授與同意，在擁有更多資訊的情況下進行決策，並協助開發人員建置更好的使用者體驗。

下圖和下表提供同意提示的建置組塊相關資訊。

![同意提示的建置組塊](./media/application-consent-experience/consent_prompt.png)

| # | 元件 | 目的 |
| ----- | ----- | ----- |
| 1 | 使用者識別碼 | 此識別碼代表所要求的用戶端應用程式使用者存取受保護的資源。 |
| 2 | 標題 | 標題變更取決於使用者是否要進行使用者或管理員同意流程。 在使用者同意流程中，標題為「所要求權限」，而在管理員同意流程中，標題則會有額外一行「為組織接受」。 |
| 3 | 應用程式標誌 | 此映像應為使用者提供視覺提示，說明此應用程式是否為使用者要存取的應用程式。 此映像由應用程式開發人員提供，且此映像的擁有權不得驗證。 |
| 4 | 應用程式名稱 | 此值應可告知使用者要求存取其資料的應用程式為何。 請注意，此名稱由應用程式開發人員提供，且此應用程式名稱的擁有權不得驗證。 |
| 5 | 發行者網域 | 此值應為使用者提供網域，以評估可信度。 此網域由應用程式開發人員提供，且此發行者網域的擁有權不得驗證。 |
| 6 | 權限 | 此清單包含用戶端應用程式所要求的權限。 使用者務必評估要求的權限類型，以了解用戶端應用程式會獲得授權的資料內容，以在其接受時代為存取。 應用程式開發人員應以最低權限要求權限的存取權。 |
| 7 | 權限描述 | 此值是由公開權限的服務所提供。 若要查看權限描述，您必須切換權限旁的 > 形箭號。 |
| 8 | 應用程式條款 | 這些條款包含應用程式的服務條款和隱私權聲明連結。 發行者需負責為其服務條款的規則製作大綱。 此外，發行者需負責揭露其使用的方式，並分享其隱私權聲明中的使用者資料。 如果發行者未將這些值的連結提供給多租用戶應用程式，同意提示即會顯示粗體警告。 |
| 9 | https://myapps.microsoft.com | 使用者可以使用這個連結檢閱和移除任何目前擁有其資料存取權的非 Microsoft 應用程式。 |

## <a name="common-consent-scenarios"></a>常見同意案例

以下是使用者可能會看到在常見同意案例中看到的同意體驗：

1. 人員存取可導向使用者同意流程的應用程式，並同時要求設定授權範圍的權限。
    
    1. 管理員會看到針對傳統同意提示的額外控制項，讓他們代表整個租用戶表示同意。 控制項預設為關閉，因此只有當管理員明確勾選方塊，才能代表整個租用戶授與同意。 目前，只有「全域管理員」角色才會看到此核取方塊，因此雲端管理員和應用程式管理員將不會看到此核取方塊。

        ![案例 1a 的同意提示](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. 使用者會看到傳統的同意提示。

        ![案例 1b 的同意提示](./media/application-consent-experience/consent_prompt_1b.png)

2. 人員存取需要至少一個授權範圍之外權限應用程式。
    1. 管理員會看到與上述 1.i 相同的提示。
    2. 使用者無法同意應用程式，並需要求管理員存取應用程式。 
                
        ![案例 1b 的同意提示](./media/application-consent-experience/consent_prompt_2b.png)

3. 人員瀏覽或導向管理員同意流程。
    1. 管理使用者會看到管理員同意提示。 此提示的標題和權限說明有所變更，該變更指出，接受此提示將代表整個租用戶將應用程式存取權授與要求的資料。
        
        ![案例 1b 的同意提示](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. 非管理使用者會看到與上述 2.ii 相同的畫面。

## <a name="next-steps"></a>後續步驟
- 取得有關 [Azure AD 同意架構如何實作同意](./quickstart-register-app.md)的逐步概觀。
- 深入了解[多租用戶應用程式如何使用同意架構](./howto-convert-app-to-be-multi-tenant.md)來實作「使用者」與「系統管理員」同意，進而支援更進階的多層應用程式模式。
- 瞭解 [如何設定應用程式的發行者網域](howto-configure-publisher-domain.md)。