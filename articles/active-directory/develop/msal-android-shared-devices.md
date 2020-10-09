---
title: 適用於 Android 裝置的共用裝置模式
titleSuffix: Microsoft identity platform | Azure
description: 瞭解如何啟用共用裝置模式，讓第一線 Worker 共用 Android 裝置
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: d9874e27c21906512c2f6c841767b4d6591dbeaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80550262"
---
# <a name="shared-device-mode-for-android-devices"></a>適用於 Android 裝置的共用裝置模式

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

第一線背景工作（例如零售夥伴、航班小組成員和現場服務工作者）通常會使用共用行動裝置來執行其工作。 當他們開始共用密碼或 pin 碼以存取共用裝置上的客戶和商務資料時，就會變成問題。

共用裝置模式可讓您設定 Android 裝置，讓多個員工輕鬆共用。 員工可以快速登入並存取客戶資訊。 當他們完成其轉移或工作時，可以登出裝置，並立即準備好供下一位員工使用。

共用裝置模式也提供受 Microsoft 身分識別支援的裝置管理。

若要建立共用裝置模式應用程式，開發人員和雲端裝置系統管理員會一起運作：

- 開發人員會撰寫單一帳戶應用程式 (在共用裝置模式下不支援多帳戶應用程式) 、新增 `"shared_device_mode_supported": true` 至應用程式的設定，以及撰寫程式碼來處理共用裝置登出之類的作業。
- 裝置系統管理員可透過安裝驗證器應用程式，並使用驗證器應用程式將裝置設定為共用模式，來準備要共用的裝置。 只有處於 [雲端裝置系統管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator-permissions) 角色的使用者可以使用 [驗證器應用程式](../user-help/user-help-auth-app-overview.md)，讓裝置進入共用模式。 您可以透過下列方式，在 Azure 入口網站中設定組織角色的成員資格： **Azure Active Directory**  >  **角色和**  >  **系統管理員雲端裝置系統管理員**。

 本文主要著重于開發人員應考慮的事項。

## <a name="single-vs-multiple-account-applications"></a>單一與多帳戶應用程式

使用 Microsoft 驗證程式庫 SDK 撰寫的應用程式 (MSAL) 可以管理單一帳戶或多個帳戶。 如需詳細資訊，請參閱 [單一帳戶模式或多重帳戶模式](single-multi-account.md)。 適用于您應用程式的 Microsoft 身分識別平臺功能會根據應用程式是在單一帳戶模式或多重帳戶模式中執行而有所不同。

**共用裝置模式應用程式只能在單一帳戶模式下運作**。

> [!IMPORTANT]
> 僅支援多重帳戶模式的應用程式無法在共用裝置上執行。 如果員工載入不支援單一帳戶模式的應用程式，它就不會在共用裝置上執行。
>
> 在 MSAL SDK 發行之前所撰寫的應用程式，會在多帳戶模式下執行，而且必須更新為支援單一帳戶模式，才能在共用模式裝置上執行。

**支援單一帳戶和多個帳戶**

您可以建立應用程式，以支援在個人裝置和共用裝置上執行。 如果您的應用程式目前支援多個帳戶，而您想要支援共用裝置模式，請新增單一帳戶模式的支援。

您也可能想要讓應用程式根據其執行所在的裝置類型來變更其行為。 用 `ISingleAccountPublicClientApplication.isSharedDevice()` 來決定要在單一帳戶模式下執行的時間。

有兩個不同的介面，代表您的應用程式所在的裝置類型。 當您從 MSAL 的應用程式 factory 要求應用程式實例時，會自動提供正確的應用程式物件。

下列物件模型說明您可能會收到的物件類型，以及在共用裝置內容中的意義：

![公用用戶端應用程式繼承模型](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

當您取得物件時，您必須進行類型檢查並轉換成適當的介面 `PublicClientApplication` 。 下列程式碼會檢查多個帳戶模式或單一帳戶模式，並適當地轉換應用程式物件：

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

下列差異取決於您的應用程式是否在共用或個人裝置上執行：

|  | 共用模式裝置  | 個人裝置 |
|---------|---------|---------|
| **帳戶**     | 單一帳戶 | 多個帳戶 |
| **登入** | 全球 | 全球 |
| **登出** | 全球 | 每個應用程式都可以控制登出是否為應用程式的本機或應用程式系列。 |
| **支援的帳戶類型** | 僅限工作帳戶 | 支援個人和工作帳戶  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>為何您可能只想要支援單一帳戶模式

如果您要撰寫的應用程式只會用於使用共用裝置的第一線背景工作，建議您將應用程式撰寫成隻支援單一帳戶模式。 這包括大部分以工作為主的應用程式，例如醫療記錄應用程式、發票應用程式，以及大多數的企業營運應用程式。 僅支援單一帳戶模式可簡化開發工作，因為您不需要執行屬於多帳戶應用程式的其他功能。

## <a name="what-happens-when-the-device-mode-changes"></a>當裝置模式變更時，會發生什麼事

如果您的應用程式是以多帳戶模式執行，且系統管理員將裝置置於共用裝置模式，則會從應用程式清除裝置上的所有帳戶，並將應用程式轉換成單一帳戶模式。

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>共用裝置登出和整體應用程式生命週期

當使用者登出時，您必須採取行動，以保護使用者的隱私權和資料。 例如，如果您正在建立醫療記錄應用程式，您會想要確定當使用者登出先前顯示的患者記錄時，會將其清除。 您的應用程式必須做好準備，並在每次進入前景時檢查。

當您的應用程式使用 MSAL 在處於共用模式的裝置上執行的應用程式中，將使用者登出時，已登入的帳戶和快取的權杖會從應用程式和裝置中移除。

下圖顯示應用程式執行時可能發生的整體應用程式生命週期和常見事件。 此圖表涵蓋活動的啟動時間、登入和登出帳戶，以及事件（例如暫停、繼續及停止活動）如何納入。

![共用裝置應用程式生命週期](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>後續步驟

請嘗試在 [您的 android 應用程式教學課程中使用共用裝置模式](tutorial-v2-shared-device-mode.md) ，以瞭解如何在共用模式的 android 裝置上執行第一線 worker 應用程式。
