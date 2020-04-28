---
title: 共用裝置模式總覽
titleSuffix: Microsoft identity platform | Azure
description: 深入瞭解共用裝置模式，為您的第一線 Worker 啟用裝置共用。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550236"
---
# <a name="overview-of-shared-device-mode"></a>共用裝置模式總覽

共用裝置模式是 Azure Active Directory 的一項功能，可讓您建立支援第一線背景工作的應用程式，並在其上部署的裝置上啟用共用裝置模式。

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-are-firstline-workers"></a>什麼是第一線 Worker？

第一線背景工作是零售員工、維護和現場代理人、醫療人員和其他不在電腦前面的使用者，或是使用公司電子郵件進行共同作業。 下列各節介紹支援第一線背景工作角色的各個層面和挑戰，然後介紹 Microsoft 提供的功能，讓您的應用程式可供組織的第一線工作者使用。

### <a name="challenges-of-supporting-firstline-workers"></a>支援第一線 Worker 的挑戰

啟用第一線背景工作流程包括一般資訊工作者通常不會顯示的挑戰。 這類挑戰可能包括高周轉率率，並較不熟悉組織的核心生產力工具。 為了加強其第一線背景工作，組織會採用不同的策略。 有些人採用攜帶您自己的裝置（BYOD）策略，讓員工在自己的電話上使用商務應用程式，而其他則為員工提供 Ipad 或 Android 平板電腦等共用裝置。

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>支援針對一位使用者設計的裝置上的多個使用者

因為執行 iOS 或 Android 的行動裝置是針對單一使用者所設計，所以大部分的應用程式都會將其使用經驗優化，供單一使用者使用。 此優化體驗的一部分表示啟用跨應用程式的單一登入，並讓使用者在其裝置上登入。 當使用者從應用程式中移除其帳戶時，應用程式通常不會將其視為安全性相關事件。 許多應用程式甚至會保留使用者的認證，以供快速登入。 當您從行動裝置刪除應用程式，然後重新安裝它時，您甚至可能會遇到這種情況，只是為了探索您仍然登入。

### <a name="global-sign-in-and-sign-out-sso"></a>全域登錄和登出（SSO）

若要讓組織的員工在這些員工共用的裝置集區上使用其應用程式，開發人員必須啟用相反的體驗。 員工應該能夠從集區挑選裝置，並在其轉移期間執行單一手勢以「使其成為其使用者」。 在其排班結束時，他們應該能夠執行另一個手勢，在裝置上全域登出，並移除所有的個人和公司資訊，讓他們可以將其傳回至裝置集區。 此外，如果員工忘記登出，裝置應該會在其移動結束時自動登出，並（或）在一段時間未使用。

Azure Active Directory 使用稱為「**共用裝置模式**」的功能來啟用這些案例。

## <a name="introducing-shared-device-mode"></a>共用裝置模式簡介

如前所述，共用裝置模式是 Azure Active Directory 的功能，可讓您：

* 建立支援第一線背景工作角色的應用程式
* 將裝置部署到第一線的工作者並開啟共用的裝置模式

### <a name="build-applications-that-support-firstline-workers"></a>建立支援第一線背景工作角色的應用程式

您可以在應用程式中使用 Microsoft Authentication Library （MSAL）和[Microsoft Authenticator 應用](../user-help/user-help-auth-app-overview.md)程式來支援第一線背景工作，以啟用稱為「*共用裝置模式*」的裝置狀態。 當裝置處於「共用裝置」模式時，Microsoft 會為您的應用程式提供資訊，讓它根據裝置上的使用者狀態修改其行為，以保護使用者資料。

支援的功能包括：

* 透過任何支援的應用程式，**以全裝置的方式登入使用者**。
* 透過任何支援的應用程式，**將整個使用者裝置登出**。
* **查詢裝置的狀態**，以判斷您的應用程式是否位於處於共用裝置模式的裝置上。
* **查詢裝置上使用者的裝置狀態**，以判斷自上次使用應用程式之後是否有任何變更。

支援共用裝置模式應視為應用程式的安全性增強功能和功能更新，並有助於增加其在醫療保健和財務等高度規範的環境中的採用。

您的使用者取決於您，以確保其資料不會洩漏給另一位使用者。 [共用裝置模式] 提供有用的信號，向您的應用程式指出您應該管理的變更已發生。 您的應用程式會負責在每次使用應用程式時檢查裝置上的使用者狀態，並清除先前的使用者資料。 這包括在多工中從背景重載的情況。 在使用者變更時，您應該確定已清除前一個使用者的資料，並移除應用程式中顯示的任何快取資料。 我們建議您在將共用裝置模式功能新增至應用程式之後，一律執行完整的安全性審查流程。

如需如何修改應用程式以支援共用裝置模式的詳細資訊，請參閱本文結尾的[後續步驟](#next-steps)一節。

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>將裝置部署到第一線的工作者並開啟共用的裝置模式

一旦您的應用程式支援共用裝置模式並包含必要的資料和安全性變更，您就可以將其公告為第一線 Worker 所能使用。

組織的裝置系統管理員能夠透過如 Microsoft Intune 的行動裝置管理（MDM）解決方案，將其裝置和您的應用程式部署到其商店和工作場所。 布建程式的一部分會將裝置標示為*共用裝置*。 系統管理員藉由部署[Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-overview.md)，並透過設定參數設定共用裝置模式，來設定共用裝置模式。 執行這些步驟之後，所有支援共用裝置模式的應用程式都將使用 Microsoft Authenticator 應用程式來管理其使用者狀態，並為裝置和組織提供安全性功能。

## <a name="next-steps"></a>後續步驟

我們支援適用于共用裝置模式的 iOS 和 Android 平臺。 請參閱下列適用于您平臺的檔，以開始在您的應用程式中支援第一線的背景工作。

* [支援 iOS 的共用裝置模式](msal-ios-shared-devices.md)
* [支援 Android 的共用裝置模式](msal-android-shared-devices.md)
