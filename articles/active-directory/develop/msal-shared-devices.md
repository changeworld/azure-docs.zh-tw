---
title: 共用裝置模式概述
titleSuffix: Microsoft identity platform | Azure
description: 瞭解共享設備模式,以便為一線工作人員啟用設備共用。
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550236"
---
# <a name="overview-of-shared-device-mode"></a>共用裝置模式概述

共用設備模式是 Azure 活動目錄的一項功能,允許您在部署到它們的設備上生成支援一線輔助功能的應用程式並啟用共用設備模式。

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-are-firstline-workers"></a>什麼是一線員工?

第一線員工是零售員工、維護和現場代理、醫務人員以及不坐在計算機前或使用公司電子郵件進行協作的其他使用者。 以下各節介紹支援一線工作人員的各個方面和挑戰,然後介紹 Microsoft 提供的功能,這些功能使您的應用程式可供組織的一線工作人員使用。

### <a name="challenges-of-supporting-firstline-workers"></a>支援一線員工的挑戰

啟用一線工作人員工作流包括典型資訊工作者通常未提出的挑戰。 這些挑戰可能包括高更替率和對組織核心生產力工具的不熟悉程度。 為了增強他們的一線員工的能力,組織正在採取不同的戰略。 一些國家正在採用自帶設備 (BYOD) 策略,其員工在其個人手機上使用業務應用,而另一些員工則為其員工提供 iPad 或 Android 平板電腦等共享設備。

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>支援為使用者設計的裝置上的多個使用者

由於運行 iOS 或 Android 的行動裝置專為單個用戶設計,因此大多數應用程式會優化其體驗,供單個使用者使用。 這種優化體驗的一部分意味著跨應用程式啟用單一登錄,並在其設備上保持用戶登錄。 當使用者從應用程式中刪除其帳戶時,應用程式通常不會將其視為與安全相關的事件。 許多應用甚至保留使用者的憑據以快速登錄。 當您從行動裝置中刪除應用程式然後重新安裝應用程式,但發現您仍處於登錄中時,您甚至可能自己體驗過這種情況。

### <a name="global-sign-in-and-sign-out-sso"></a>全域登入與登出 (SSO)

為了允許組織員工在這些員工共用的設備池中使用其應用,開發人員需要啟用相反的體驗。 員工應該能夠從池中挑選設備,並在輪班期間執行單個手勢以"使其成為自己的設備"。 輪班結束時,他們應該能夠執行另一個手勢,在設備上全域註銷,並刪除其所有個人和公司資訊,以便將其返回到設備池。 此外,如果員工忘記註銷,設備應在輪班結束時和/或處於非活動狀態后自動註銷。

Azure 活動目錄使用稱為**共享設備模式**的功能啟用這些方案。

## <a name="introducing-shared-device-mode"></a>引入共享裝置模式

如前所述,共用設備模式是 Azure 活動目錄的一項功能,使您能夠:

* 產生支援一線工作人員的應用程式
* 將裝置部署到一線工作人員並開啟共享裝置模式

### <a name="build-applications-that-support-firstline-workers"></a>產生支援一線工作人員的應用程式

您可以使用 Microsoft 身份驗證庫 (MSAL) 和[Microsoft 身份驗證器應用](../user-help/user-help-auth-app-overview.md)來啟用稱為*共用裝置模式*的設備狀態,從而支援應用程式中的第一線工作人員。 當設備處於共用設備模式時,Microsoft 會向應用程式提供資訊,以允許它根據設備上的使用者狀態修改其行為,從而保護用戶數據。

支援的功能包括:

* 透過任何受支援的應用程式**在使用者裝置範圍內登入**。
* 透過任何受支援的應用程式**在使用者裝置上登出**。
* **查詢裝置的狀態,** 以確定應用程式是否位於處於共用設備模式的設備上。
* **查詢設備上使用者的設備狀態**,以確定自上次使用應用程式以來是否發生了任何更改。

支援共享設備模式應被視為應用程式的安全增強和功能升級,並有助於在高度監管的環境中(如醫療保健和財務)中增加其採用率。

您的用戶依賴您來確保其數據不會洩露給其他使用者。 共用設備模式提供有用的信號,以向應用程式指示您應該管理的更改已發生。 每次使用應用時,應用程式負責檢查設備上的用戶狀態,從而清除前一個用戶的數據。 這包括如果在多任務處理中從後台重新載入它,則包括它。 在使用者更改時,應確保清除前一個用戶的數據,並刪除應用程式中顯示的任何緩存數據。 我們建議您在將共用設備模式功能添加到應用后,始終執行徹底的安全檢查過程。

有關如何修改應用程式以支援共享設備模式的詳細資訊,請參閱本文末尾的[「後續步驟](#next-steps)」部分。

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>將裝置部署到一線工作人員並開啟共享裝置模式

一旦應用程式支援共享設備模式並包含所需的數據和安全更改,就可以宣傳它們可供一線工作人員使用。

組織的設備管理員能夠透過 Microsoft Intune 等行動裝置管理 (MDM) 解決方案將其設備和應用程式部署到其商店和工作場所。 預先設定的一部份是將裝置標記為*共享裝置*。 管理員透過部署[Microsoft驗證器應用](../user-help/user-help-auth-app-overview.md)並透過設定參數設定共享設備模式來配置共用設備模式。 執行這些步驟後,支援共享設備模式的所有應用程式都將使用 Microsoft 身份驗證器應用程式來管理其用戶狀態,並為設備和組織提供安全功能。

## <a name="next-steps"></a>後續步驟

我們支援 iOS 和 Android 平臺,支援共享設備模式。 請查看以下文檔,瞭解您的平臺,以開始在應用程式中支援一線工作人員。

* [支援 iOS 的共享裝置模式](msal-ios-shared-devices.md)
* [支援 Android 的共享裝置模式](msal-android-shared-devices.md)
