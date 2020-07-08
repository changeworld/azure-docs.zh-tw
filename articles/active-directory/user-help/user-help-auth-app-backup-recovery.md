---
title: 使用 Microsoft Authenticator 應用程式備份和復原帳戶 - Azure AD
description: 了解如何使用 Microsoft Authenticator 應用程式備份和復原所備份的帳戶認證。
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: b13de16b2080d28d3b779921456cfb20d99f0af9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84415970"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 應用程式備份和復原帳戶認證

**適用範圍：**

- 執行 5.7.0 版和以後版本的 iOS 裝置

- 執行 6.6.0 版和以後版本的 Android 裝置

Microsoft Authenticator 應用程式會將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 備份之後，您也可以使用此應用程式在新裝置上復原您的資訊，這有可能避免遭到鎖定或者必須重新建立帳戶。

每個備份儲存位置都需要有一個 Microsoft 個人帳戶，iOS 也會要求擁有 iCloud 帳戶。 您可以將多個帳戶儲存在該單一位置。 例如，您可以有個人帳戶、公司或學校帳戶，以及 Facebook、Google 等非 Microsoft 的個人帳戶。

> [!IMPORTANT]
> 系統只會儲存您的個人帳戶認證和協力廠商帳戶認證資訊，包括您的使用者名稱與證明身分識別所需的帳戶驗證碼。 我們不會儲存其他任何與您帳戶相關的資訊，包括電子郵件或檔案。 也不會以任何方式將您的帳戶與其他產品或服務建立關聯或共用您的帳戶。 而且您的 IT 管理員並不會取得上述任何帳戶的任何相關資訊。

## <a name="back-up-your-account-credentials"></a>備份您的帳戶認證

您必須先具備下列項目，才能備份您的認證：

- 個人 [Microsoft 帳戶](https://account.microsoft.com/account)作為復原帳戶。

- **僅限 iOS**：必須具備實際儲存位置的 [iCloud 帳戶](https://www.icloud.com/)。

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>開啟 iOS 裝置的雲端備份

- 在您的 iOS 裝置上，選取 [設定]、[備份]，然後開啟 [iCloud 備份]。

    您的帳戶認證會備份到您的 iCloud 帳戶。

    ![iOS 設定畫面，其中顯示 iCloud 備份設定的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>開啟 Android 裝置的雲端備份

- 在您的 Android 裝置上，選取 [設定]、[備份]，然後開啟 [雲端備份]。

    您的帳戶認證會備份到您的雲端帳戶。

    ![Android 設定畫面，其中顯示自動備份設定的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>在您的新裝置上復原您的帳戶認證

您可以從雲端帳戶復原您的帳戶憑證，但必須先確定要復原的帳戶不存在於 Microsoft Authenticator 應用程式中。 例如，如果您要復原個人 Microsoft 帳戶，則必須確定您在 Authenticator 應用程式內尚未設定任何個人 Microsoft 帳戶。 這項檢查很重要，如此才能確定保障不會意外覆寫或清除現有的帳戶。

### <a name="to-recover-your-information"></a>復原您的資訊

1. 在您的行動裝置上開啟 Microsoft Authenticator 應用程式，然後選取畫面底部的 [開始復原]。

    ![Microsoft Authenticator 應用程式，其中顯示可按一下 [開始復原] 的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. 使用您在備份過程中所使用的相同個人 Microsoft 帳戶，登入您的復原帳戶。

    您的帳戶認證會復原到新的裝置。

完成復原之後，您可能會注意到舊電話與新電話上 Microsoft Authenticator 應用程式中的個人 Microsoft 帳戶驗證碼不同。 驗證碼不同，因為每個裝置都有自己的唯一認證，但是在使用相關聯的電話進行登入時兩者均有效且有作用。

## <a name="recover-accounts-requiring-more-verification"></a>復原需要更多驗證的帳戶

如果您使用推播通知搭配您的個人、公司或學校帳戶，您會在畫面上看到一則警示，指出您必須先提供額外的驗證，才可以復原您的資訊。 由於推播通知需要使用已繫結至特定裝置且永遠不會透過網路傳送的認證，所以您必須先證明您的身分識別，才能在裝置上建立認證。

對於個人 Microsoft 帳戶，您可輸入您的密碼以及替代電子郵件或電話號碼來證明您的身分識別。 針對公司或學校帳戶，您必須掃描帳戶提供者提供給您的 QR 代碼。

### <a name="to-provide-more-verification-for-personal-accounts"></a>為個人帳戶提供更多驗證

1. 在 Microsoft Authenticator 應用程式的 [**帳戶**] 畫面中，按一下您要復原的帳戶，以開啟該帳戶的全螢幕視圖。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="Microsoft Authenticator 應用程式，顯示可用的帳戶磚" border="true":::

1. 針對您要復原的帳戶，按 [磚]，然後按一下 [登入以進行復原] 選項。 輸入您的密碼，然後確認您的電子郵件地址或電話號碼，以做為額外的驗證。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="驗證器會提供一次性密碼作為驗證碼" border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>為公司或學校帳戶提供更多驗證

1. 在 Microsoft Authenticator 應用程式的 [**帳戶**] 畫面中，按一下您要復原的帳戶，以開啟該帳戶的全螢幕視圖。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="Microsoft Authenticator 應用程式，顯示可用的帳戶磚" border="true":::

1. 在全螢幕視圖中，按一下 [掃描 QR 代碼以完全復原] 選項。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="驗證器會提供一次性密碼作為驗證碼" border="true":::

>[!NOTE]
>如需進一步了解 QR 代碼及取得方式，請參閱[開始使用 Microsoft Authenticator 應用程式](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)或[將安全性資訊設定為使用驗證器應用程式](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) (取決於系統管理員是否已開啟安全性資訊)。
>
>如果這是您第一次設定 Microsoft Authenticator 應用程式，系統可能會顯示提示詢問您是否允許應用程式存取您的相機 (iOS)，或是否允許應用程式拍攝相片和錄製影片 (Android)。 您必須選取 [允許]，讓驗證器應用程式能夠存取您的相機，以在下一個步驟中拍攝 QR 代碼的相片。 如果您不允許存取相機，您仍可設定驗證器應用程式，但必須手動新增代碼資訊。 如需如何手動新增代碼的相關資訊，請參閱[手動新增帳戶至應用程式](user-help-auth-app-add-account-manual.md)。

## <a name="troubleshoot-backup-and-recovery-problems"></a>備份和復原問題的疑難排解

您的備份無法使用的可能原因如下

- **變更作業系統**：您的備份儲存於 iOS 版 iCloud 以及 Microsoft 的 Android 版雲端儲存體提供者內。 因此如果在 Android 和 iOS 裝置之間切換，就無法使用您的備份。 如果切換裝置，則必須在 Microsoft Authenticator 應用程式內手動重新建立您的帳戶。

- **網路問題**：如果遇到網路相關問題，請確定已連線到網路且正確登入您的帳戶。

- **帳戶問題**：如果遇到帳戶相關問題，請確定已正確登入您的帳戶。 若為 iOS，則表示必須使用與 iPhone 相同的 AppleID 帳戶登入 iCloud。

- **意外刪除**：您可能已經刪除了先前裝置上的備份帳戶，或在管理雲端儲存空間帳戶時刪除。 在此情況下，您必須在此應用程式內手動重新建立您的帳戶。

- **現有的 Microsoft Authenticator 帳戶**：如果您已經在 Microsoft Authenticator 應用程式中設定帳戶，則應用程式將無法復原您已備份的帳戶。 防止復原有助於確保過時資訊不會覆寫您的帳戶詳細資料。 在此情況下，您必須先從在 Microsoft Authenticator 應用程式中設定的現有帳戶移除任何現有的帳戶資訊，才可以復原您的備份。

- **備份已過時**：如果您的備份資訊已過期，系統可能會要求您再次登入您的 Microsoft 復原帳戶，以重新整理資訊。 您的復原帳戶是您最初用來儲存備份的個人 Microsoft 帳戶。 如果需要登入，您會在功能表或動作列上看到一個紅點，或者您會看到一個驚嘆號圖示，提示您登入以完成從備份還原。 選取適當的圖示之後，系統會提示您再次登入以更新您的資訊。

## <a name="next-steps"></a>後續步驟

既然您已將帳戶認證備份和復原至新裝置，即可繼續使用 Microsoft Authenticator 應用程式來驗證您的身分識別。 如需詳細資訊，請參閱[使用 Microsoft Authenticator 應用程式登入帳戶](user-help-sign-in.md)。

## <a name="related-articles"></a>相關文章

- [什麼是 Microsoft Authenticator 應用程式？](user-help-auth-app-overview.md)

- [Microsoft Authenticator 應用程式常見問題集](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
