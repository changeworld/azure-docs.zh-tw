---
title: 條件式存取原則中的會話控制項-Azure Active Directory
description: 什麼是 Azure AD 條件式存取原則中的會話控制項
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
ms.openlocfilehash: 84c794e6fe751bc1c12b90353ef7b285f31a2331
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192138"
---
# <a name="conditional-access-session"></a>條件式存取：會話

在條件式存取原則中，系統管理員可以利用會話控制項來啟用特定雲端應用程式中有限的體驗。

![具有需要多重要素驗證之 grant 控制項的條件式存取原則](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>應用程式強制執行限制

組織可以使用此控制項來要求 Azure AD 將裝置資訊傳遞給選取的雲端應用程式。 裝置資訊可讓雲端應用程式知道連線是否從符合規範或已加入網域的裝置起始。 此控制項僅支援 SharePoint Online 和 Exchange Online 作為選取的雲端應用程式。 選取後，雲端應用程式會使用裝置資訊來提供使用者有限或完整的體驗 (視裝置狀態而定)。

如需應用程式強制執行限制的使用和設定詳細資訊，請參閱下列文章：

- [啟用 SharePoint Online 的有限存取](https://docs.microsoft.com/sharepoint/control-access-from-unmanaged-devices)
- [啟用 Exchange Online 的有限存取](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>條件式存取應用程式控制

條件式存取應用程式控制使用反向 proxy 架構，並與 Azure AD 條件式存取進行唯一整合。 Azure AD 條件式存取可讓您根據特定條件，在組織的應用程式上強制執行存取控制。 條件會定義要套用條件式存取原則的物件（使用者或使用者群組）和內容（哪些雲端應用程式）和位置（哪些位置和網路）。 判斷條件之後，您可以將使用者路由傳送至[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) ，您可以透過套用存取和會話控制項，使用條件式存取應用程式控制來保護資料。

條件式存取應用程式控制可以根據存取和會話原則，即時監視和控制使用者應用程式存取和會話。 在 Cloud App Security 入口網站中使用存取和會話原則，以進一步精簡篩選並設定要對使用者採取的動作。 使用存取和會話原則，您可以：

- 防止資料外泄：例如，您可以在未受管理的裝置上封鎖下載、剪下、複製及列印敏感性檔。
- 在下載時保護：不封鎖下載機密檔，您可以要求將檔加上標籤並受到 Azure 資訊保護保護。 此動作可確保檔受到保護，並在可能有風險的會話中限制使用者存取。
- 禁止上傳未標記的檔案：上傳、散佈和使用機密檔案之前，請務必確定該檔案具有適當的標籤和保護。 您可以確保在使用者分類內容之前，會封鎖具有機密內容的未標記檔案，使其無法上傳。
- 監視使用者會話的相容性：有風險的使用者會在登入應用程式時受到監視，並從會話內記錄其動作。 您可以調查和分析使用者行為，以瞭解未來應該在哪些情況下套用會話原則。
- 封鎖存取：您可以根據數個風險因素，以細微的方式封鎖特定應用程式和使用者的存取。 例如，如果使用用戶端憑證作為裝置管理的形式，您可以封鎖它們。
- 封鎖自訂活動：有些應用程式具有具有風險的獨特案例，例如，在應用程式（如 Microsoft 小組或寬限）中傳送具有機密內容的訊息。 在這種情況下，您可以掃描郵件中的機密內容，並即時加以封鎖。

如需詳細資訊，請參閱[部署精選應用程式的條件式存取應用程式控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)一文。

## <a name="sign-in-frequency-preview"></a>登入頻率（預覽）

登入頻率會定義在嘗試存取資源時，要求使用者重新登入的時間週期。

登入頻率設定適用于已根據標準來執行 OAUTH2 或 OIDC 通訊協定的應用程式。 大部分適用于 Windows、Mac 和行動裝置的 Microsoft 原生應用程式，包括下列 web 應用程式都符合設定。

- Word、Excel、PowerPoint Online
- OneNote Online
- Office.com
- O365 系統管理員入口網站
- Exchange Online
- SharePoint 和 OneDrive
- 小組 web 用戶端
- Dynamics CRM Online
- Azure 入口網站

如需詳細資訊，請參閱[使用條件式存取來設定驗證會話管理](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)一文。

## <a name="persistent-browser-session-preview"></a>持續性瀏覽器會話（預覽）

持續性瀏覽器會話可讓使用者在關閉並重新開啟其瀏覽器視窗之後，保持登入狀態。

如需詳細資訊，請參閱[使用條件式存取來設定驗證會話管理](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)一文。

## <a name="next-steps"></a>後續步驟

- [條件式存取的一般原則](concept-conditional-access-policy-common.md)

- [僅限報表模式](concept-conditional-access-report-only.md)
