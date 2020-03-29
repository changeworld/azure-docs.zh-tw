---
title: 條件訪問策略中的會話控制項 - Azure 活動目錄
description: Azure AD 條件訪問策略中的會話控制項是什麼
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671895"
---
# <a name="conditional-access-session"></a>條件訪問：會話

在條件訪問策略中，管理員可以使用會話控制項在特定雲應用程式中啟用有限的體驗。

![具有需要多重要素驗證的授予控制的條件訪問策略](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>應用程式強制限制

組織可以使用此控制項要求 Azure AD 將設備資訊傳遞到選定的雲應用。 裝置資訊可讓雲端應用程式知道連線是否從符合規範或已加入網域的裝置起始。 此控制項僅支援 SharePoint Online 和 Exchange Online 作為選取的雲端應用程式。 選取後，雲端應用程式會使用裝置資訊來提供使用者有限或完整的體驗 (視裝置狀態而定)。

有關應用強制限制的使用和配置的詳細資訊，請參閱以下文章：

- [啟用 SharePoint Online 的有限存取](/sharepoint/control-access-from-unmanaged-devices)
- [啟用 Exchange Online 的有限存取](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>條件訪問應用程式控制

條件訪問應用控制項使用反向代理體系結構，並且與 Azure AD 條件訪問唯一集成。 Azure AD 條件式存取可讓您根據特定條件，對貴組織的應用程式強制執行存取控制。 條件會定義要套用條件式存取原則的對象 (使用者或使用者群組)、項目 (哪些雲端應用程式) 和位置 (哪些位置和網路)。 確定條件後，可以將使用者路由到[Microsoft 雲應用安全，](/cloud-app-security/what-is-cloud-app-security)通過應用訪問和會話控制項，您可以使用條件訪問應用控制保護資料。

條件式存取應用程式控制可根據存取和工作階段原則，即時監視與控制使用者應用程式存取和工作階段。 Cloud App Security 入口網站內使用存取和工作段原則來進一步細化篩選並設定要對使用者採取的動作。 使用存取和工作階段原則，您可以：

- 防止資料滲漏：您可以阻止在非託管設備上下載、剪切、複製和列印敏感文檔。
- 保護下載：您可以要求使用 Azure 資訊保護標記和保護文檔，而不是阻止敏感文檔的下載。 此操作可確保文檔受到保護，並在存在潛在風險的會話中限制使用者訪問。
- 防止上載未標記的檔：在敏感檔被他人上載、分發和使用之前，請務必確保該檔具有正確的標籤和保護。 您可以確保在使用者對內容進行分類之前，阻止上載包含敏感內容的未標記檔。
- 監視使用者會話的合規性：風險使用者在登錄到應用時受到監視，並且其操作從會話中記錄。 您可以調查及分析使用者的行為，以了解未來應該在什麼位置及什麼情況下套用工作階段原則。
- 阻止訪問：您可以根據多種風險因素，精細地阻止特定應用和使用者的訪問。 例如，如果它們使用用戶端憑證作為裝置管理的一種形式，則可以阻止它們。
- 阻止自訂活動：某些應用具有具有風險的獨特方案，例如，在 Microsoft Teams 或 Slack 等應用中發送包含敏感內容的消息。 在此類方案中，您可以掃描消息以尋找敏感內容並即時阻止它們。

有關詳細資訊，請參閱為[特色應用部署條件訪問應用控制項](/cloud-app-security/proxy-deployment-aad)的文章。

## <a name="sign-in-frequency-preview"></a>登錄頻率（預覽）

登錄頻率定義在嘗試訪問資源時要求使用者重新登錄之前的時間段。

登錄頻率設置適用于已根據標準實現 OAUTH2 或 OIDC 協定的應用。 大多數適用于 Windows、Mac 和行動裝置的 Microsoft 本機應用（包括以下 Web 應用程式）都符合該設置。

- 字， Excel， 電源點線上
- 一個筆記線上
- Office.com
- O365 監管中心
- Exchange Online
- SharePoint 與 OneDrive
- 團隊 Web 用戶端
- Dynamics CRM Online
- Azure 入口網站

有關詳細資訊，請參閱文章["使用條件訪問配置身份驗證會話管理](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)"。

## <a name="persistent-browser-session-preview"></a>持久瀏覽器會話（預覽）

持久性瀏覽器會話允許使用者在關閉和重新打開瀏覽器視窗後保持登錄狀態。

有關詳細資訊，請參閱文章["使用條件訪問配置身份驗證會話管理](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)"。

## <a name="next-steps"></a>後續步驟

- [條件訪問通用策略](concept-conditional-access-policy-common.md)

- [僅限報告模式](concept-conditional-access-report-only.md)
