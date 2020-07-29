---
title: 設定安全性金鑰作為您的驗證方法 - Azure AD
description: 如何設定您的 [安全性資訊] (預覽) 頁面來驗證您的身分識別，以使用線上快速身分識別 (FIDO2) 安全性金鑰作為驗證方法。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: 91138f104c787148042b187f4cf59a60bc1448d3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744422"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>設定安全性金鑰作為您的驗證方法

您可以使用安全性金鑰作為組織內的無密碼登入方法。 安全性金鑰是一種實體裝置，搭配不重複的專屬 PIN 使用，用以登入您的公司或學校帳戶。 由於安全性金鑰需要擁有實體裝置，而且需要只有您知道的資訊，因此視為強度比使用者名稱和密碼更高的驗證方法。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> 如果您未看到安全性金鑰選項，可能是您的組織不允許您使用這個選項進行驗證。 在這種情況下，您必須選擇其他方法，或連絡貴組織的技術支援中心以取得更多協助。

## <a name="security-verification-versus-password-reset-authentication"></a>安全性驗證與密碼重設驗證

雙因素安全性驗證和密碼重設都會使用安全性資訊方法。 不過，並非所有方法都同時適用於這兩者。

| 方法 | 用於 |
| ------ | -------- |
| 驗證器應用程式 | 雙因素驗證與密碼重設驗證。 |
| 簡訊 | 雙因素驗證與密碼重設驗證。 |
| 電話 | 雙因素驗證與密碼重設驗證。 |
| 安全性金鑰 | 雙因素驗證與密碼重設驗證。 |
| 電子郵件帳戶 | 僅適用於密碼重設驗證。 您必須選擇另一種方法來進行雙因素驗證。 |
| 安全性問題 | 僅適用於密碼重設驗證。 您必須選擇另一種方法來進行雙因素驗證。 |

## <a name="what-is-a-security-key"></a>什麼是安全性金鑰？

我們目前支援多種使用[線上快速身分識別 (FIDO)](https://fidoalliance.org/fido2/) (FIDO2) 無密碼驗證通訊協定的安全性金鑰設計與供應商。 您可以透過這種金鑰，在支援的裝置和網頁瀏覽器上登入您的公司或學校帳戶，以存取組織的雲端式資源。

您的管理員或組織如果要求您的公司或學校帳戶使用安全性金鑰，即會提供安全性金鑰給您。 有各種不同類型的安全性金鑰可供使用，例如插入裝置的 USB 金鑰，或是用於輕觸 NFC 讀取器的 NFC 金鑰。 如需更多安全性金鑰的相關資訊 (例如其所屬的類型)，請參閱製造商的文件。

> [!Note]
> 如果您無法使用 FIDO2 安全性金鑰，還有其他無密碼驗證方法可供使用，例如 Microsoft Authenticator 應用程式或 Windows Hello。 如需 Microsoft Authenticator 應用程式的詳細資訊，請參閱[什麼是 Microsoft Authenticator 應用程式？](user-help-auth-app-overview.md) 如需 Windows Hello 的詳細資訊，請參閱 [Windows Hello 概觀](https://www.microsoft.com/windows/windows-hello)。

## <a name="before-you-begin"></a>開始之前

您必須符合下列條件，才能註冊安全性金鑰：

- 您的管理員已開啟這項功能以供組織使用。

- 您使用的裝置執行 Windows 10 May 2019 Update 並使用支援的瀏覽器。

- 您擁有由您的管理員或組織核准的實體安全性金鑰。 您的安全性金鑰必須同時相容於 FIDO2 和 Microsoft。 如果您對安全性金鑰有任何疑問，或有相容性方面的問題，請連絡貴組織的技術支援中心。

## <a name="register-your-security-key"></a>註冊安全性金鑰

您必須先建立安全性金鑰，並設定不重複的專屬的 PIN，才能使用該金鑰登入您的公司或學校帳戶。 您的帳戶最多可以註冊 10 個金鑰。 

1. 移至 https://myaccount.microsoft.com 的  [我的設定檔] 頁面 (如果尚未登入的話，請登入)。

2. 選取 [安全性資訊]，選取 [新增方法]，再於 [新增方法] 下拉式清單中選取 [安全性金鑰]。

    ![[新增方法] 方塊，其中已選取 [安全性金鑰]](media/security-info/security-info-security-key-add-method.png)

3. 選取 [新增]，然後選取您擁有的安全性金鑰類型，可能是 [USB 裝置] 或 [NFC 裝置]。

    ![選擇您的安全性金鑰類型是 USB 還是 NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 如果不確定擁有的是何種安全性金鑰，請參閱製造商文件。 如果不確定製造商，請連絡組織的技術支援中心以取得協助。

4. 準備好您實體的安全性金鑰，然後在 [安全性金鑰] 方塊中，選取 [下一步]。

    ![安全性金鑰開始註冊方塊](media/security-info/security-info-security-key-start-setup.png)

    會顯示新的方塊，協助您設定新的登入方法。

5. 在 [設定新的登入方法] 方塊中，選取 [下一步]，接下來：

    - 如果您的安全性金鑰是 USB 裝置，請將安全性金鑰插入裝置的 USB 連接埠。

    - 如果您的安全性金鑰是 NFC 裝置，請用安全性金鑰輕觸讀取器。

6. 在 [Windows 安全性] 方塊中輸入您專屬的安全性金鑰 PIN，然後選取 [確定]。

    您會回到 [設定新的登入方法] 方塊。

7. 選取 [下一步] 。

8. 返回 [安全性資訊] 頁面，輸入日後用來辨識新安全性金鑰的名稱，然後選取 [下一步]。

    ![安全性資訊頁面，命名您的安全性金鑰](media/security-info/security-info-security-key-name.png)

    您的安全性金鑰已經註冊完成，可用來登入您的公司或學校帳戶。

9. 按一下 [完成] 關閉 [安全性金鑰] 方塊。

    會以您的安全性金鑰資訊更新 [安全性資訊]。

    ![[安全性資訊] 頁面，顯示了所有已註冊的方法](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>將安全性金鑰從安全性資訊刪除

如果您誤放了安全性金鑰或不想繼續使用，可以從安全性資訊刪除金鑰。 如此該安全性金鑰就再也無法搭配您的公司或學校帳戶使用，但安全性金鑰仍會儲存您的資料和認證資訊。 若要將您的資料和認證資訊從安全性金鑰本身刪除，請務必按照本文中[重設 Microsoft 相容安全性金鑰](#reset-your-security-key)一節中的指示操作。

1. 在要移除的安全性金鑰中選取 [刪除] 連結。

2. 在 [刪除安全性金鑰] 方塊中選取 [確定]。

    您的安全性金鑰已刪除，您無法繼續使用該金鑰登入您的公司或學校帳戶。

>[!Important]
>如果不小心刪除了安全性金鑰，可按照本文中[註冊安全性金鑰](#register-your-security-key)一節的指示再次註冊。

## <a name="manage-your-security-key-settings-from-windows-settings"></a>於 Windows 設定管理安全性金鑰設定

您可以在 **Windows 設定**應用程式中管理安全性金鑰設定，包括重設安全性金鑰，以及建立新的安全性金鑰 PIN。

### <a name="reset-your-security-key"></a>重設安全性金鑰

如果想要刪除儲存在實體安全性金鑰上的所有帳戶資訊，則必須將金鑰恢復至其原廠預設值。 重設安全性金鑰會刪除金鑰中的所有內容，一切從頭開始。

>[!IMPORTANT]
>重設您的安全性金鑰會刪除金鑰中的所有內容，將其重設為原廠預設值。
>
> **將會清除所有資料和認證。**

#### <a name="to-reset-your-security-key"></a>重設安全性金鑰

1. 開啟 Windows 設定應用程式，選取 [帳戶]，選取 [登入選項]，選取 [安全性金鑰]，再選取 [管理]。

2. 將您的安全性金鑰插入 USB 連接埠，或輕觸您的 NFC 讀取器，以驗證您的身分識別。

3. 請依照安全性金鑰製造商，按螢幕指示進行。 如果您的金鑰製造商未列在螢幕指示中，請參照製造商網站取得詳細資訊。

4. 選取 [關閉] 來關閉  [管理] 畫面。

### <a name="create-a-new-security-key-pin"></a>建立新的安全性金鑰 PIN

您可以為安全性金鑰建立新的安全性金鑰 PIN。

#### <a name="to-create-a-new-security-key-pin"></a>建立新的安全性金鑰 PIN

1. 開啟 Windows 設定應用程式，選取 [帳戶]，選取 [登入選項]，選取 [安全性金鑰]，再選取 [管理]。

2. 將您的安全性金鑰插入 USB 連接埠，或輕觸您的 NFC 讀取器，以驗證您的身分識別。
3. 在 [安全性金鑰 PIN] 區域中選取 [新增]，確認您的新安全性金鑰 PIN，然後選取 [確定]。

     系統會用新的安全性金鑰 PIN 更新安全性金鑰，以便搭配您的公司或學校帳戶使用。 如果決定要再次變更您的 PIN，請選取 [變更] 按鈕。
4. 選取 [關閉] 來關閉  [管理] 畫面。

## <a name="additional-security-info-methods"></a>其他安全性資訊方法

若要註冊安全性金鑰，必須至少再註冊一種安全性驗證方法。 如需詳細資訊，請參閱[概觀](security-info-add-update-methods-overview.md)一節。 

## <a name="next-steps"></a>後續步驟

- 如需無密碼驗證方法的相關詳細資訊，請參閱 [Microsoft 的 Azure AD 開始公開預覽 FIDO2 安全性金鑰供無密碼登入](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) (英文) 部落格文章，或參閱[什麼是 Microsoft Authenticator 應用程式？](user-help-auth-app-overview.md)和 [Windows Hello 概觀](https://www.microsoft.com/windows/windows-hello)文章。

- 更多 [Microsoft 相容的安全性金鑰](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)關詳細資訊。

- 如果您遺失或忘記密碼，請從[密碼重設入口網站](https://passwordreset.microsoftonline.com/)或遵循[重設公司或學校密碼](active-directory-passwords-update-your-own-password.md)一文中的步驟來重設密碼。

- 在[無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中取得登入問題的疑難排解秘訣和說明。
