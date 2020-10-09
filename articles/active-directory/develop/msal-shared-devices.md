---
title: 共用裝置模式總覽
titleSuffix: Microsoft identity platform | Azure
description: 深入瞭解共用裝置模式，為您的第一線背景工作啟用裝置共用。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80550236"
---
# <a name="overview-of-shared-device-mode"></a>共用裝置模式的總覽

共用裝置模式是 Azure Active Directory 的功能，可讓您建立支援第一線背景工作的應用程式，並在部署至裝置的裝置上啟用共用裝置模式。

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-are-firstline-workers"></a>什麼是第一線 Worker？

第一線 Worker 是零售員工、維護和現場專員、醫療人員，以及不在電腦前面的其他使用者，或使用公司電子郵件進行共同作業。 下列各節介紹支援第一線背景工作的各方面和挑戰，以及 Microsoft 提供的功能簡介，讓組織的第一線背景工作能夠使用您的應用程式。

### <a name="challenges-of-supporting-firstline-workers"></a>支援第一線背景工作的挑戰

啟用第一線背景工作流程，通常不是由一般資訊工作者所提供。 這類挑戰可能包括高營業額率，而且較不熟悉組織的核心生產力工具。 為了加強其第一線背景工作，組織採用不同的策略。 有些人採用攜帶您自己的裝置 (BYOD) 策略，讓員工在他們的個人電話上使用商務應用程式，有些則為員工提供 Ipad 或 Android 平板電腦等共用裝置。

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>支援為一位使用者設計的裝置上的多個使用者

由於執行 iOS 或 Android 的行動裝置是針對單一使用者所設計，因此大部分的應用程式都會將其體驗優化，以供單一使用者使用。 這項優化體驗的一部分，表示在應用程式之間啟用單一登入，並讓使用者在其裝置上保持登入。 當使用者從應用程式移除其帳戶時，應用程式通常不會將其視為安全性相關事件。 許多應用程式甚至會保留使用者的認證，以供快速登入之用。 當您從行動裝置中刪除應用程式，然後重新安裝它時，您可能會遇到這種情況，只是為了探索您仍處於登入狀態。

### <a name="global-sign-in-and-sign-out-sso"></a>全域登入和登出 (SSO) 

為了讓組織的員工能夠在這些員工所共用的裝置集區上使用其應用程式，開發人員必須啟用相反的體驗。 員工應該能夠從集區中挑選裝置，並在其變換的持續時間執行單一手勢以「讓它成為他們」。 在移動結束時，他們應該能夠執行另一個手勢，以在裝置上全域登出，並移除其所有的個人和公司資訊，讓他們可以將其退回裝置集區。 此外，如果員工忘記登出，裝置應該會在其排班結束時自動登出，並在一段時間內停止活動之後自動登出。

Azure Active Directory 使用稱為 **共用裝置模式**的功能來啟用這些案例。

## <a name="introducing-shared-device-mode"></a>共用裝置模式簡介

如前所述，共用裝置模式是 Azure Active Directory 的功能，可讓您：

* 建立支援第一線背景工作的應用程式
* 將裝置部署至第一線背景工作角色並開啟共用裝置模式

### <a name="build-applications-that-support-firstline-workers"></a>建立支援第一線背景工作的應用程式

您可以使用 Microsoft 驗證程式庫 (MSAL) 和 [Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-overview.md) 來啟用稱為 *共用裝置模式*的裝置狀態，以支援您應用程式中的第一線背景工作。 當裝置處於共用裝置模式時，Microsoft 會將資訊提供給您的應用程式，讓它根據裝置上使用者的狀態來修改其行為，以保護使用者資料。

支援的功能包括：

* 透過任何支援的應用程式登**入使用者全裝置**。
* 透過任何支援的應用程式，**登出使用者全裝置**。
* **查詢裝置的狀態** ，以判斷您的應用程式是否位於處於共用裝置模式的裝置上。
* **查詢裝置上使用者的裝置狀態** ，以判斷自從上次使用您的應用程式之後是否有任何變更。

支援共用裝置模式應該同時被視為應用程式的安全性增強功能和功能更新，並且有助於提高其在醫療保健和財務等高度管制環境中的採用。

您的使用者取決於您，以確保其資料不會洩漏給另一位使用者。 共用裝置模式提供有用的信號，向應用程式指出您應該管理的變更已發生。 您的應用程式會負責在每次使用應用程式時，檢查裝置上的使用者狀態，並清除前一個使用者的資料。 這包括在多工中從背景重載時。 在使用者變更時，您應該確定已清除前一個使用者的資料，而且會移除應用程式中顯示的所有快取資料。 建議您在將共用裝置模式功能新增至您的應用程式之後，一律執行徹底的安全性審查流程。

如需如何修改您的應用程式以支援共用裝置模式的詳細資訊，請參閱本文結尾的 [後續步驟](#next-steps) 一節。

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>將裝置部署至第一線背景工作角色並開啟共用裝置模式

一旦您的應用程式支援共用裝置模式，並包含必要的資料和安全性變更，您就可以將它們公告為第一線背景工作可供使用。

組織的裝置系統管理員可以透過行動裝置管理 (MDM) 解決方案（例如 Microsoft Intune），將其裝置和您的應用程式部署到其商店和工作場所。 布建程式的一部分會將裝置標示為 *共用裝置*。 系統管理員可以藉由部署 [Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-overview.md) ，並透過設定參數設定共用裝置模式，來設定共用裝置模式。 執行這些步驟之後，所有支援共用裝置模式的應用程式都會使用 Microsoft Authenticator 應用程式來管理其使用者狀態，並提供裝置和組織的安全性功能。

## <a name="next-steps"></a>後續步驟

我們支援 iOS 和 Android 平臺的共用裝置模式。 請參閱下列檔以瞭解您的平臺，以開始在您的應用程式中支援第一線背景工作。

* [支援 iOS 的共用裝置模式](msal-ios-shared-devices.md)
* [支援 Android 的共用裝置模式](msal-android-shared-devices.md)
