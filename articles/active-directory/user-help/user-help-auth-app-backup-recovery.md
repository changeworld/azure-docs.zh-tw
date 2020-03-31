---
title: 使用 Microsoft 身份驗證器應用備份和恢復帳戶 - Azure AD
description: 瞭解如何使用 Microsoft 身份驗證器應用備份和恢復備份的帳戶憑據。
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78297955"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>使用 Microsoft 身份驗證器應用備份和恢復帳戶憑據

**適用於：**

- iOS 設備，運行版本 5.7.0 及更高版本

- Android 設備，運行版本 6.6.0 及更高版本

Microsoft Authenticator 應用程式會將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 備份之後，您也可以使用此應用程式在新裝置上復原您的資訊，這有可能避免遭到鎖定或者必須重新建立帳戶。

每個備份存儲位置要求您擁有一個個人 Microsoft 帳戶，而 iOS 也要求您擁有 iCloud 帳戶。 您可以在該單個位置存儲多個帳戶。 例如，您可以擁有個人帳戶、工作或學校帳戶，以及個人非 Microsoft 帳戶，如 Facebook、Google 等。

> [!IMPORTANT]
> 僅存儲您的個人和協力廠商帳戶憑據，其中包括您的使用者名和證明您的身份所需的帳戶驗證碼。 我們不會存儲與您的帳戶關聯的任何其他資訊，包括電子郵件或檔。 我們也不會以任何方式與任何其他產品或服務關聯或共用您的帳戶。 最後，您的 IT 管理員不會獲取有關任何這些帳戶的任何資訊。

## <a name="back-up-your-account-credentials"></a>備份您的帳戶認證

在備份憑據之前，您必須具有：

- 個人 [Microsoft 帳戶](https://account.microsoft.com/account)作為復原帳戶。

- **僅對於 iOS，** 您必須擁有實際存儲位置的[iCloud 帳戶](https://www.icloud.com/)。

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>打開 iOS 設備的雲備份

- 在您的 iOS 裝置上，選取 [設定]****、[備份]****，然後開啟 [iCloud 備份]****。

    您的帳戶認證會備份到您的 iCloud 帳戶。

    ![iOS 設定畫面，其中顯示 iCloud 備份設定的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>打開 Android 設備的雲備份

- 在 Android 設備上，選擇 **"設置"，** 選擇 **"備份**"，然後打開**雲備份**。

    您的帳戶憑據將備份到您的雲帳戶。

    ![Android 設置螢幕，顯示備份設置的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>在您的新裝置上復原您的帳戶認證

可以從雲帳戶中恢復帳戶憑據，但首先必須確保正在恢復的帳戶不存在在 Microsoft 身份驗證器應用中。 例如，如果要恢復個人 Microsoft 帳戶，必須確保在身份驗證器應用中沒有已設置的個人 Microsoft 帳戶。 此檢查很重要，因此我們可以確保我們不會錯誤地覆蓋或擦除現有帳戶。

### <a name="to-recover-your-information"></a>復原您的資訊

1. 在行動裝置上，打開 Microsoft 身份驗證器應用，然後從螢幕底部選擇 **"開始恢復**"。

    ![Microsoft Authenticator 應用程式，其中顯示可按一下 [開始復原] 的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. 使用您在備份過程中所使用的相同個人 Microsoft 帳戶，登入您的復原帳戶。

    您的帳戶認證會復原到新的裝置。

完成復原之後，您可能會注意到舊電話與新電話上 Microsoft Authenticator 應用程式中的個人 Microsoft 帳戶驗證碼不同。 驗證碼不同，因為每個裝置都有自己的唯一認證，但是在使用相關聯的電話進行登入時兩者均有效且有作用。

## <a name="recover-additional-accounts-requiring-more-verification"></a>復原其他需要更多驗證的帳戶

如果您將推送通知用於個人或工作或學校帳戶，您將獲得螢幕警報，指出必須先提供其他驗證，然後才能恢復您的資訊。 由於推送通知需要使用綁定到特定設備且從未通過網路發送的憑據，因此必須在設備上創建憑據之前證明您的身份。

對於個人 Microsoft 帳戶，您可輸入您的密碼以及替代電子郵件或電話號碼來證明您的身分識別。 針對公司或學校帳戶，您必須掃描帳戶提供者提供給您的 QR 代碼。

### <a name="to-provide-additional-verification-for-personal-accounts"></a>提供額外的個人帳戶驗證

1. 在 Microsoft 身份驗證器應用的 **"帳戶"** 螢幕中，選擇要恢復的帳戶。 在 Android 設備上，選擇要恢復的帳戶旁邊的箭頭。

    ![Microsoft Authenticator 應用程式，其中顯示可用的帳戶及其相關聯的下拉式箭號](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    在 iOS 設備上，點擊要恢復的帳戶以打開帳戶的全屏視圖。

    ![Microsoft Authenticator 應用程式，其中顯示可用的帳戶及其相關聯的下拉式箭號](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. 登錄以恢復帳戶。 在 Android 設備上，選擇 **"登錄以恢復**"。

    ![微軟身份驗證器應用程式，以在 Android 上輸入您的登錄資訊](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    在 iOS 設備上，點按要恢復的帳戶磁貼，然後點擊登錄和恢復的選項。 然後鍵入您的密碼，然後確認您的電子郵件地址或電話號碼作為其他驗證。

    ![Microsoft 身份驗證器應用，用於在 iOS 上輸入您的登錄資訊](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>提供額外的公司或學校帳戶驗證

1. 登錄以恢復帳戶。 在 Android 設備上，選擇 **"登錄以恢復**"。

    ![微軟身份驗證器應用程式恢復在 Android 上的工作或學校帳戶](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    在 iOS 設備上，點擊要恢復的帳戶以打開帳戶的全屏視圖。

    ![微軟身份驗證器應用在 iOS 上恢復工作或學校帳戶](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. 您可以使用 QR 碼恢復帳戶。 選取 [掃描 QR 代碼進行復原]****，然後掃描 QR 代碼。

    在 Android 上：

    ![微軟身份驗證器應用程式在Android上，允許您掃描你的QR碼](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    在 iOS 上：

    ![iOS 上的 Microsoft 身份驗證器應用，允許您掃描 QR 碼](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >有關 QR 碼以及如何獲取 QR 代碼的詳細資訊，請參閱[從 Microsoft 身份驗證器應用開始](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)，或根據管理員是否打開安全資訊設置[安全資訊以使用身份驗證器應用](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)。
    >
    >如果這是您第一次設定 Microsoft Authenticator 應用程式，系統可能會顯示提示詢問您是否允許應用程式存取您的相機 (iOS)，或是否允許應用程式拍攝相片和錄製影片 (Android)。 您必須選取 [允許]****，讓驗證器應用程式能夠存取您的相機，以在下一個步驟中拍攝 QR 代碼的相片。 如果您不允許存取相機，您仍可設定驗證器應用程式，但必須手動新增代碼資訊。 如需如何手動新增代碼的相關資訊，請參閱[手動新增帳戶至應用程式](user-help-auth-app-add-account-manual.md)。

## <a name="troubleshoot-backup-and-recovery-problems"></a>排除備份和恢復問題

備份可能不可用的原因有幾個

- **更改作業系統**：您的備份存儲在 iCloud 中，用於 iOS 和 Microsoft 的 Android 雲存儲供應商中。 這意味著，如果您在 Android 和 iOS 設備之間切換，您的備份將不可用。 如果進行切換，則必須在 Microsoft 身份驗證器應用中手動重新創建帳戶。

- **網路問題**：如果您遇到與網路相關的問題，請確保您已連接到網路並正確登錄到您的帳戶。

- **帳戶問題**：如果您遇到與帳戶相關的問題，請確保您已正確登錄到您的帳戶。 對於 iOS，這意味著您必須使用與 iPhone 相同的 AppleID 帳戶登錄 iCloud。

- **意外刪除**：您可能會從以前的設備中刪除備份帳戶，或者在管理雲存儲帳戶時刪除備份帳戶。 在此情況下，您必須在此應用程式內手動重新建立您的帳戶。

- **現有的 Microsoft 身份驗證器帳戶**：如果您已在 Microsoft 身份驗證器應用中設置了帳戶，則該應用將無法恢復備份的帳戶。 防止復原有助於確保過時資訊不會覆寫您的帳戶詳細資料。 在此情況下，您必須先從在 Microsoft Authenticator 應用程式中設定的現有帳戶移除任何現有的帳戶資訊，才可以復原您的備份。

- **備份已過期**：如果您的備份資訊過期，系統可能會要求您通過再次登錄到 Microsoft 恢復帳戶來刷新資訊。 您的恢復帳戶是您最初用於存儲備份的個人 Microsoft 帳戶。 如果需要登錄，您將在功能表或動作列上看到一個紅點，或者您將看到一個驚嘆號，提示您登錄以完成從備份恢復。 選擇該圖示後，系統將提示您再次登錄以更新您的資訊。

## <a name="next-steps"></a>後續步驟

既然您已將帳戶認證備份和復原至新裝置，即可繼續使用 Microsoft Authenticator 應用程式來驗證您的身分識別。 有關詳細資訊，請參閱使用[Microsoft 身份驗證器應用登錄您的帳戶](user-help-sign-in.md)。

## <a name="related-articles"></a>相關文章

- [什麼是 Microsoft Authenticator 應用程式？](user-help-auth-app-overview.md)

- [Microsoft Authenticator 應用程式常見問題集](user-help-auth-app-faq.md)

- [多重要素驗證](https://docs.microsoft.com/azure/multi-factor-authentication/)
