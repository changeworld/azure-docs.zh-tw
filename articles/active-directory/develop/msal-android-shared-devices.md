---
title: 適用於 Android 裝置的共用裝置模式
titleSuffix: Microsoft identity platform | Azure
description: 瞭解如何啟用共用設備模式,以允許一線工作人員共用 Android 設備
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
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550262"
---
# <a name="shared-device-mode-for-android-devices"></a>適用於 Android 裝置的共用裝置模式

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

一線員工(如零售員工、機組人員和現場服務人員)通常使用共用行動裝置完成工作。 當他們開始共享密碼或 PIN 號以造訪共享裝置上的客戶和業務數據時,這就成問題了。

共用設備模式允許您配置 Android 設備,以便多個員工可以輕鬆共用該設備。 員工可以快速登錄和訪問客戶資訊。 當他們完成輪班或任務后,他們可以註銷設備,並且將立即為下一個員工使用做好準備。

共用設備模式還提供 Microsoft 標識支援的設備管理。

要建立共享設備模式應用,開發人員和雲端裝置管理員將協同工作:

- 開發人員編寫單帳戶應用(共用設備模式下不支援多帳戶應用),添加到`"shared_device_mode_supported": true`應用的配置,並編寫代碼來處理共用設備註銷等內容。
- 設備管理員透過安裝身份驗證器應用並使用身分驗證器應用將設備設置為共用模式來準備要共用的設備。 只有處於[雲端裝置管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator-permissions)角色的使用者才能使用[身份驗證器應用](../user-help/user-help-auth-app-overview.md)將設備置於共用模式。 您可以通過 **:Azure 活動目錄** > **角色和管理員** > **雲設備管理員**在 Azure 門戶中配置組織角色的成員身份。

 本文主要集中於開發人員應該考慮的內容。

## <a name="single-vs-multiple-account-applications"></a>單帳戶與多帳戶應用程式

使用 Microsoft 身份驗證庫 SDK (MSAL) 編寫的應用程式可以管理單個帳戶或多個帳戶。 有關詳細資訊,請參閱[單帳戶模式或多帳戶模式](single-multi-account.md)。 適用於你的應用的 Microsoft 識別平臺功能因應用程式是在單帳戶模式還是多帳戶模式下運行而異。

**共用裝置模式應用僅在單帳戶模式下工作**。

> [!IMPORTANT]
> 僅支援多帳戶模式的應用程式無法在共用設備上運行。 如果員工載入不支援單帳戶模式的應用,則它不會在共用設備上運行。
>
> 在 MSAL SDK 發佈之前編寫的應用以多帳戶模式運行,必須先更新以支援單帳戶模式,然後才能在共用模式設備上運行。

**支援單一帳號和多個帳戶**

可以構建應用以支援在個人設備和共享設備上運行。 如果應用當前支援多個帳戶,並且希望支援共享設備模式,請添加對單一帳戶模式的支援。

您可能還希望應用根據運行的設備類型更改其行為。 用於`ISingleAccountPublicClientApplication.isSharedDevice()`確定何時在單一帳戶模式下運行。

有兩個不同的介面表示應用程式打開的設備類型。 當您從 MSAL 的應用程式工廠請求應用程式實例時,將自動提供正確的應用程式物件。

以下物件模型說明瞭您可能會接收的物件類型以及它在共享裝置的上下文中的含義:

![公開用戶端應用程式繼承模型](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

獲取`PublicClientApplication`物件時,需要執行類型檢查並強制轉換為相應的介面。 以下代碼檢查多個帳戶模式或單帳戶模式,並適當地強制強制轉換應用程式物件:

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

以下差異適用於應用是否在共享裝置或個人裝置上執行:

|  | 共用模式裝置  | 個人裝置 |
|---------|---------|---------|
| **帳戶**     | 單一帳戶 | 多個帳戶 |
| **登入** | 全域 | 全域 |
| **登出** | 全域 | 每個應用程式都可以控制登出是應用還是應用程式系列的本地。 |
| **支援的帳戶類型** | 僅限工作帳戶 | 支援個人帳戶和工作帳戶  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>為什麼您可能只想支援單一帳戶模式

如果您正在編寫僅用於使用共享設備的一線工作人員的應用,我們建議您將應用程式編寫為僅支援單帳戶模式。 這包括大部分以工作為主的應用程式，例如醫療記錄應用程式、發票應用程式，以及大多數的企業營運應用程式。 僅支援單帳戶模式即可簡化開發,因為您不需要實現屬於多帳戶應用的其他功能。

## <a name="what-happens-when-the-device-mode-changes"></a>裝置模式變更時會發生什麼情況

如果應用程式在多帳戶模式下運行,並且管理員將設備置於共用設備模式,則設備上的所有帳戶都將從應用程式清除,應用程式將轉換為單帳戶模式。

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>共用裝置登出與整個應用程式生命週期

當使用者退出時,您需要採取措施保護用戶的隱私和數據。 例如,如果要構建醫療記錄應用,則需要確保當使用者退出以前顯示的患者記錄時被清除。 您的應用程式必須為此做好準備,並在每次進入前臺時進行檢查。

當應用使用 MSAL 在處於共用模式的設備上運行的應用中登出使用者時,將從應用和設備中刪除登錄帳戶和緩存權杖。

下圖顯示了應用運行時可能發生的總體應用生命週期和常見事件。 該圖涵蓋從活動啟動、登錄和註銷帳戶的時間,以及暫停、恢復和停止活動等事件如何配合。

![共用裝置應用生命週期](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>後續步驟

在[Android 應用程式教程中嘗試使用共用設備模式](tutorial-v2-shared-device-mode.md),該教程演示如何在共用模式 Android 設備上運行一線輔助應用。
