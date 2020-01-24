---
title: 適用于 Android 裝置的共用裝置模式 |Azure
description: 瞭解共用裝置模式，讓第一線背景工作角色可以共用 Android 裝置
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: twhitney
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 53fa82cf6eaaba09353ba21a12ae9677b9264b1a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701462"
---
# <a name="shared-device-mode-for-android-devices"></a>適用於 Android 裝置的共用裝置模式

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

第一線的工作者（例如零售協會、航班小組成員，以及現場服務工作者）通常會使用共用的行動裝置來執行其工作。 當他們開始共用密碼或 pin 號碼以存取共用裝置上的客戶和商務資料時，就會發生問題。

共用裝置模式可讓您設定 Android 裝置，讓多個員工可以輕鬆地共用它。 員工可以快速登入並存取客戶資訊。 當他們完成其轉移或工作時，可以登出裝置，並立即準備好供下一位員工使用。

共用裝置模式也提供對裝置進行 Microsoft 身分識別支援的管理。

若要建立共用裝置模式應用程式，開發人員和雲端裝置管理員會共同作業：

- 開發人員撰寫單一帳戶應用程式（共用裝置模式中不支援多帳戶應用程式）、將 `"shared_device_mode_supported": true` 新增至應用程式的設定，以及撰寫程式碼來處理像是共用裝置登出之類的專案。
- 裝置系統管理員會藉由安裝驗證器應用程式來準備要共用的裝置，並使用驗證器應用程式將裝置設定為共用模式。 只有[雲端裝置系統管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator)角色的使用者可以使用[驗證器應用程式](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)，讓裝置進入共用模式。 您可以透過下列方式，在 Azure 入口網站中設定組織角色的成員資格： **Azure Active Directory** > **角色和**系統管理員 > **雲端裝置管理員**。

 本文主要著重于開發人員應該考慮的事項。

## <a name="single-vs-multiple-account-applications"></a>單一 vs 多重帳戶應用程式

使用 Microsoft 驗證程式庫 SDK （MSAL）所撰寫的應用程式可以管理單一帳戶或多個帳戶。 如需詳細資訊，請參閱[單一帳戶模式或多帳戶模式](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account)。 您的應用程式可用的 Microsoft 身分識別平臺功能，會根據應用程式是以單一帳戶模式或多帳戶模式執行而有所不同。

**共用裝置模式應用程式只能在單一帳戶模式下工作**。

> [!IMPORTANT]
> 僅支援多帳戶模式的應用程式不能在共用裝置上執行。 如果員工載入不支援單一帳戶模式的應用程式，它就不會在共用裝置上執行。
>
> MSAL SDK 發行之前所撰寫的應用程式會以多帳戶模式執行，而且必須更新以支援單一帳戶模式，才能在共用模式裝置上執行。

**同時支援單一帳戶和多個帳戶**

您的應用程式可以建立為支援在個人裝置和共用裝置上執行。 如果您的應用程式目前支援多個帳戶，而您想要支援共用裝置模式，請新增單一帳戶模式的支援。

您可能也會想要讓您的應用程式根據其執行所在的裝置類型來變更其行為。 使用 `ISingleAccountPublicClientApplication.isSharedDevice()` 來判斷何時以單一帳戶模式執行。

有兩個不同的介面，代表您的應用程式所在的裝置類型。 當您從 MSAL 的應用程式 factory 要求應用程式實例時，會自動提供正確的應用程式物件。

下列物件模型說明您可能會收到的物件類型，以及它在共用裝置內容中的意義：

![公用用戶端應用程式繼承模型](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

當您取得 `PublicClientApplication` 物件時，您必須進行型別檢查並轉換成適當的介面。 下列程式碼會檢查是否有多個帳戶模式或單一帳戶模式，並適當地轉換應用程式物件：

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

下列差異取決於您的應用程式是在共用或個人裝置上執行：

|  | 共用模式裝置  | 個人裝置 |
|---------|---------|---------|
| **帳戶**     | 單一帳戶 | 多個帳戶 |
| **登入** | 全球 | 全球 |
| **登出** | 全球 | 每個應用程式都可以控制登出應用程式的本機或應用程式系列。 |
| **支援的帳戶類型** | 僅限工作帳戶 | 支援個人和公司帳戶  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>為什麼您可能只想要支援單一帳戶模式

如果您撰寫的應用程式只會用於使用共用裝置的第一線背景工作，我們建議您撰寫應用程式，只支援單一帳戶模式。 這包括大部分以工作為主的應用程式，例如醫療記錄應用程式、發票應用程式，以及大多數的企業營運應用程式。 僅支援單一帳戶模式可簡化開發，因為您不需要執行屬於多帳戶應用程式一部分的其他功能。

## <a name="what-happens-when-the-device-mode-changes"></a>當裝置模式變更時，會發生什麼事

如果您的應用程式是以多帳戶模式執行，而且系統管理員將裝置設為共用裝置模式，則會從應用程式清除裝置上的所有帳戶，而應用程式會轉換成單一帳戶模式。

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>共用裝置登出和整體應用程式生命週期

當使用者登出時，您必須採取動作來保護使用者的隱私權和資料。 例如，如果您要建立醫療記錄應用程式，您會想要確保在使用者登出先前顯示的患者記錄時，會進行清除。 您的應用程式必須準備好進行此工作，並在每次進入前景時檢查。

當您的應用程式使用 MSAL 在共用模式的裝置上執行的應用程式中登出使用者時，會從應用程式和裝置中移除登入帳戶和快取權杖。

下圖顯示應用程式執行時可能發生的整體應用程式生命週期和一般事件。 此圖表涵蓋活動啟動、登入和登出帳戶的時間，以及事件（例如暫停、繼續和停止活動）如何納入。

![共用裝置應用程式生命週期](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>後續步驟

請嘗試在[android 應用程式中使用共用裝置模式](tutorial-v2-shared-device-mode.md)教學課程，以瞭解如何在共用模式的 android 裝置上執行第一線 worker 應用程式。
