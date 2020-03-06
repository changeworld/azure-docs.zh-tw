---
title: 使用 Microsoft Authenticator 應用程式來備份和復原帳戶 Azure AD
description: 瞭解如何使用 Microsoft Authenticator 應用程式來備份和復原備份的帳號憑證。
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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78297955"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 應用程式來備份和復原帳號憑證

**適用範圍：**

- 執行5.7.0 版和更新版本的 iOS 裝置

- 執行6.6.0 和更新版本的 Android 裝置

Microsoft Authenticator 應用程式會將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 備份之後，您也可以使用此應用程式在新裝置上復原您的資訊，這有可能避免遭到鎖定或者必須重新建立帳戶。

每個備份儲存位置都需要有一個個人 Microsoft 帳戶，而 iOS 也會要求您擁有 iCloud 帳戶。 您可以將多個帳戶儲存在該單一位置。 例如，您可以擁有個人帳戶、工作或學校帳戶，以及個人、非 Microsoft 帳戶（例如 Facebook、Google 等等）。

> [!IMPORTANT]
> 只會儲存您的個人和協力廠商帳號憑證，包括您的使用者名稱和證明您身分識別所需的帳戶驗證碼。 我們不會儲存任何與您的帳戶相關聯的其他資訊，包括電子郵件或檔案。 我們也不會以任何方式或任何其他產品或服務，與您的帳戶建立關聯或共用。 最後，您的 IT 系統管理員將不會取得任何這些帳戶的相關資訊。

## <a name="back-up-your-account-credentials"></a>備份您的帳戶認證

您必須先擁有下列內容，才能備份您的認證：

- 個人 [Microsoft 帳戶](https://account.microsoft.com/account)作為復原帳戶。

- **僅適用于 iOS，** 針對實際的儲存位置，您必須擁有[iCloud 帳戶](https://www.icloud.com/)。

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>開啟 iOS 裝置的雲端備份

- 在您的 iOS 裝置上，選取 [設定]、[備份]，然後開啟 [iCloud 備份]。

    您的帳戶認證會備份到您的 iCloud 帳戶。

    ![iOS 設定畫面，其中顯示 iCloud 備份設定的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>開啟 Android 裝置的雲端備份

- 在您的 Android 裝置上，選取 [**設定**]，選取 [**備份**]，然後開啟 [**雲端備份**]。

    您的帳號憑證會備份至您的雲端帳戶。

    ![[Android 設定] 畫面，顯示備份設定的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>在您的新裝置上復原您的帳戶認證

您可以從雲端帳戶復原您的帳號憑證，但必須先確定您要復原的帳戶不存在於 Microsoft Authenticator 應用程式中。 例如，如果您要復原個人 Microsoft 帳戶，則必須確定您在驗證器應用程式中沒有已設定的個人 Microsoft 帳戶。 這項檢查很重要，因此我們可以確定我們不會錯誤地覆寫或清除現有的帳戶。

### <a name="to-recover-your-information"></a>復原您的資訊

1. 在您的行動裝置上，開啟 Microsoft Authenticator 應用程式，然後從畫面底部選取 [**開始**復原]。

    ![Microsoft Authenticator 應用程式，其中顯示可按一下 [開始復原] 的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. 使用您在備份過程中所使用的相同個人 Microsoft 帳戶，登入您的復原帳戶。

    您的帳戶認證會復原到新的裝置。

完成復原之後，您可能會注意到舊電話與新電話上 Microsoft Authenticator 應用程式中的個人 Microsoft 帳戶驗證碼不同。 驗證碼不同，因為每個裝置都有自己的唯一認證，但是在使用相關聯的電話進行登入時兩者均有效且有作用。

## <a name="recover-additional-accounts-requiring-more-verification"></a>復原其他需要更多驗證的帳戶

如果您搭配個人或公司或學校帳戶使用推播通知，將會出現畫面上警示，指出您必須先提供額外的驗證，才能復原您的資訊。 因為推播通知需要使用系結至特定裝置的認證，而且永遠不會透過網路傳送，所以您必須證明您的身分識別，才能在裝置上建立認證。

對於個人 Microsoft 帳戶，您可輸入您的密碼以及替代電子郵件或電話號碼來證明您的身分識別。 針對公司或學校帳戶，您必須掃描帳戶提供者提供給您的 QR 代碼。

### <a name="to-provide-additional-verification-for-personal-accounts"></a>提供額外的個人帳戶驗證

1. 在 Microsoft Authenticator 應用程式的 [**帳戶**] 畫面中，選取您要復原的帳戶。 在 Android 裝置上，選取您要復原之帳戶旁的箭號。

    ![Microsoft Authenticator 應用程式，其中顯示可用的帳戶及其相關聯的下拉式箭號](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    在 iOS 裝置上，請按一下您想要復原的帳戶，以開啟該帳戶的全螢幕視圖。

    ![Microsoft Authenticator 應用程式，其中顯示可用的帳戶及其相關聯的下拉式箭號](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. 登入以復原帳戶。 在 Android 裝置上，選取 [登**入以復原**]。

    ![Microsoft Authenticator 應用程式在 Android 上輸入您的登入資訊](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    在 iOS 裝置上，依序按一下您想要復原的帳戶磚，然後按一下 [登入和復原] 選項。 然後輸入您的密碼，然後確認您的電子郵件地址或電話號碼是否為額外的驗證。 unt。

    ![Microsoft Authenticator 應用程式在 iOS 上輸入您的登入資訊](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>提供額外的公司或學校帳戶驗證

1. 登入以復原帳戶。 在 Android 裝置上，選取 [登**入以復原**]。

    ![Microsoft Authenticator 應用程式在 Android 上復原工作或學校帳戶](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    在 iOS 裝置上，請按一下您想要復原的帳戶，以開啟該帳戶的全螢幕視圖。

    ![Microsoft Authenticator 應用程式在 iOS 上復原工作或學校帳戶](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. 您可以使用 QR 代碼來復原帳戶。 選取 [掃描 QR 代碼進行復原]，然後掃描 QR 代碼。

    在 Android 上：

    ![在 Android 上 Microsoft Authenticator 應用程式，可讓您掃描 QR 代碼](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    在 iOS 上：

    ![在 iOS 上 Microsoft Authenticator 應用程式，可讓您掃描 QR 代碼](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >如需 QR 代碼的詳細資訊以及如何取得它，請參閱[開始使用 Microsoft Authenticator 應用程式](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)或[將安全性資訊設定為使用驗證器應用程式](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)（根據您的系統管理員是否已開啟安全性資訊）。
    >
    >如果這是您第一次設定 Microsoft Authenticator 應用程式，系統可能會顯示提示詢問您是否允許應用程式存取您的相機 (iOS)，或是否允許應用程式拍攝相片和錄製影片 (Android)。 您必須選取 [允許]，讓驗證器應用程式能夠存取您的相機，以在下一個步驟中拍攝 QR 代碼的相片。 如果您不允許存取相機，您仍可設定驗證器應用程式，但必須手動新增代碼資訊。 如需如何手動新增代碼的相關資訊，請參閱[手動新增帳戶至應用程式](user-help-auth-app-add-account-manual.md)。

## <a name="troubleshoot-backup-and-recovery-problems"></a>針對備份和復原問題進行疑難排解

您的備份可能無法使用的原因有好幾個

- **變更作業系統**：您的備份會儲存在適用于 IOS 的 iCloud 和適用于 Android 的 Microsoft 雲端存放裝置提供者中。 這表示如果您在 Android 和 iOS 裝置之間切換，則無法使用您的備份。 如果您進行切換，則必須在 Microsoft Authenticator 應用程式內手動重新建立您的帳戶。

- **網路問題**：如果您遇到網路相關問題，請確定您已連線到網路，並已正確登入您的帳戶。

- **帳戶問題**：如果您遇到帳戶相關的問題，請確定您已正確登入您的帳戶。 在 iOS 中，這表示您必須使用與 iPhone 相同的 AppleID 帳戶登入 iCloud。

- **意外刪除**：您可能會從先前的裝置刪除備份帳戶，或在管理您的雲端儲存體帳戶時將其刪除。 在此情況下，您必須在此應用程式內手動重新建立您的帳戶。

- **現有的 Microsoft Authenticator 帳戶**：如果您已經在 Microsoft Authenticator 應用程式中設定帳戶，應用程式將無法復原備份的帳戶。 防止復原有助於確保過時資訊不會覆寫您的帳戶詳細資料。 在此情況下，您必須先從在 Microsoft Authenticator 應用程式中設定的現有帳戶移除任何現有的帳戶資訊，才可以復原您的備份。

- **備份已過期**：如果您的備份資訊已過期，系統可能會要求您重新登入您的 Microsoft 復原帳戶，以重新整理資訊。 您的復原帳戶是您最初用來儲存備份的個人 Microsoft 帳戶。 如果需要登入，您會在功能表或動作列上看到一個紅點，或者您會看到一個驚嘆號，提示您登入以完成從備份還原。 選取圖示後，系統會提示您再次登入以更新您的資訊。

## <a name="next-steps"></a>後續步驟

既然您已將帳戶認證備份和復原至新裝置，即可繼續使用 Microsoft Authenticator 應用程式來驗證您的身分識別。 如需詳細資訊，請參閱[使用 Microsoft Authenticator 應用程式登入您的帳戶](user-help-sign-in.md)。

## <a name="related-articles"></a>相關文章

- [什麼是 Microsoft Authenticator 應用程式？](user-help-auth-app-overview.md)

- [Microsoft Authenticator 應用程式常見問題集](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
