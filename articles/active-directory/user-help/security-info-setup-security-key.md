---
title: 將安全金鑰設置為驗證方法 - Azure AD
description: 如何設置安全資訊（預覽）頁面以驗證您的身份，以使用快速線上身份驗證 （FIDO2） 安全金鑰作為驗證方法。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062331"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>將安全金鑰設置為驗證方法

您可以在組織中使用安全金鑰作為無密碼登錄方法。 安全金鑰是一種物理設備，它使用唯一 PIN 登錄您的工作或學校帳戶。 由於安全金鑰要求您擁有物理設備和僅您知道的內容，因此它被視為比使用者名和密碼更強的驗證方法。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> 如果看不到安全金鑰選項，則您的組織可能不允許使用此選項進行驗證。 在這種情況下，您需要選擇其他方法或聯繫組織的説明台以獲得更多説明。

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

## <a name="what-is-a-security-key"></a>什麼是安全金鑰？

目前，我們使用[快速線上識別 （FIDO） （FIDO）](https://fidoalliance.org/fido2/) （FIDO2） 無密碼身份驗證協定支援多個安全金鑰設計和提供程式。 這些金鑰允許您登錄到工作或學校帳戶，以便在受支援的設備和 Web 瀏覽器上訪問組織的基於雲的資源。

如果您的管理員或您的組織需要安全金鑰用於您的工作或學校帳戶，則他們將會為您提供安全金鑰。 您可以使用不同類型的安全金鑰，例如插入設備的 USB 金鑰或點擊 NFC 讀卡機的 NFC 金鑰。 您可以從製造商的文檔中瞭解有關安全金鑰的詳細資訊，包括安全金鑰的類型。

> [!Note]
> 如果您無法使用 FIDO2 安全金鑰，則可以使用其他無密碼驗證方法，如 Microsoft 身份驗證器應用或 Windows Hello。 有關 Microsoft 身份驗證器應用的詳細資訊，請參閱什麼是[Microsoft 身份驗證器應用？](user-help-auth-app-overview.md) 有關 Windows Hello 的詳細資訊，請參閱[Windows Hello 概述](https://www.microsoft.com/windows/windows-hello)。

## <a name="before-you-begin"></a>開始之前

在能夠註冊安全金鑰之前，必須正確：

- 管理員已打開此功能，供組織內使用。

- 您正在運行 Windows 10 2019 更新的設備上，並使用受支援的瀏覽器。

- 您有管理員或組織批准的物理安全金鑰。 您的安全金鑰必須同時符合 FIDO2 和 Microsoft 要求。 如果您對安全金鑰及其是否相容有任何疑問，請與組織的説明台聯繫。

## <a name="register-your-security-key"></a>註冊安全金鑰

您必須創建安全金鑰並給它一個唯一的 PIN，然後才能使用該金鑰登錄到您的工作或學校帳戶。 您的帳戶中最多可能註冊了 10 個金鑰。 

1. 轉到"**我的個人資料"** 頁面https://myprofile.microsoft.com，如果您尚未登錄。"

2. 選擇 **"安全資訊**"，選擇 **"添加方法**"，然後從 **"添加方法**下拉清單"中選擇 **"安全金鑰**"。

    ![添加方法框，選擇安全鍵](media/security-info/security-info-security-key-add-method.png)

3. 選擇 **"添加**"，然後選擇您擁有的安全金鑰類型，即**USB 設備**或 NFC**設備**。

    ![選擇您是否具有 USB 或 NFC 類型的安全金鑰](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 如果您不確定您擁有哪種類型的安全金鑰，請參閱製造商的文檔。 如果您不確定製造商，請與組織的説明台聯繫以獲得説明。

4. 使安全金鑰在物理上可用，然後在 **"安全金鑰"** 框中，選擇 **"下一步**"。

    ![安全金鑰啟動註冊框](media/security-info/security-info-security-key-start-setup.png)

    一個新框似乎可以説明您完成設置新的登錄方法。

5. 在 **"設置新登錄方法**"框中，選擇 **"下一步**"，然後選擇：

    - 如果您的安全金鑰是 USB 設備，請將安全金鑰插入設備的 USB 埠。

    - 如果您的安全金鑰是 NFC 設備，請點按您的安全金鑰到您的讀者。

6. 在**Windows 安全**框中鍵入唯一的安全金鑰 PIN，然後選擇 **"確定**"。

    您將返回到 **"設置新登錄方法"** 框。

7. 選取 [下一步]****。

8. 返回到 **"安全資訊**"頁，鍵入稍後新安全金鑰將識別的名稱，然後選擇 **"下一步**"。

    ![安全資訊頁面，命名安全金鑰](media/security-info/security-info-security-key-name.png)

    您的安全金鑰已註冊，可供您使用登錄您的工作或學校帳戶。

9. 選擇 **"完成"** 以關閉**安全金鑰**框。

    **安全資訊**頁面將更新您的安全金鑰資訊。

    ![安全資訊頁面，顯示所有已註冊方法](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>從安全資訊中刪除安全金鑰

如果放置錯誤或不再想要使用安全金鑰，則可以從安全資訊中刪除該金鑰。 雖然這阻止安全金鑰與工作或學校帳戶一起使用，但安全金鑰將繼續存儲您的資料和憑據資訊。 要從安全金鑰本身中刪除您的資料和憑據資訊，必須按照本文的["重置 Microsoft 相容安全金鑰](#reset-your-security-key)"部分中的說明進行操作。

1. 從安全金鑰中選擇 **"刪除**"連結以將其刪除。

2. 從 **"刪除安全金鑰"** 框中選擇 **"確定**"。

    您的安全金鑰已被刪除，您將無法再使用它登錄到您的工作或學校帳戶。

>[!Important]
>如果錯誤地刪除了安全金鑰，則可以使用本文"[如何註冊安全金鑰](#register-your-security-key)"部分中的說明再次註冊安全金鑰。

## <a name="manage-your-security-key-settings-from-windows-settings"></a>從 Windows 設置管理安全金鑰設置

您可以通過 Windows 設置應用管理安全金鑰**設置**，包括重置安全金鑰和創建新的安全金鑰 PIN。

### <a name="reset-your-security-key"></a>重置安全金鑰

如果要刪除存儲在物理安全金鑰上的所有帳戶資訊，則必須將金鑰返回到其出廠預設值。 重置安全金鑰會從金鑰中刪除所有內容，從而允許您重新開始。

>[!IMPORTANT]
>重置安全金鑰會刪除從金鑰到出廠預設值的所有內容。
>
> **將清除所有資料和憑據。**

#### <a name="to-reset-your-security-key"></a>重置安全金鑰

1. 打開 Windows 設置應用，選擇 **"帳戶**"，選擇 **"登錄"選項**，選擇 **"安全金鑰**"，然後選擇"**管理**"。

2. 將安全金鑰插入 USB 埠或點擊 NFC 讀卡機以驗證您的身份。

3. 根據您的特定安全金鑰製造商，按照螢幕上的說明進行操作。 如果螢幕說明中未列出您的金鑰製造商，請參閱製造商網站瞭解更多資訊。

4. 選擇 **"關閉**"以關閉 **"管理"** 螢幕。

### <a name="create-a-new-security-key-pin"></a>創建新的安全金鑰 PIN

您可以為您的安全金鑰創建新的安全金鑰 PIN。

#### <a name="to-create-a-new-security-key-pin"></a>創建新的安全金鑰 PIN

1. 打開 Windows 設置應用，選擇 **"帳戶**"，選擇 **"登錄"選項**，選擇 **"安全金鑰**"，然後選擇"**管理**"。

2. 將安全金鑰插入 USB 埠或點擊 NFC 讀卡機以驗證您的身份。
3. 選擇"從**安全金鑰 PIN**區域**添加**"，鍵入並確認新的安全金鑰 PIN，然後選擇 **"確定**"。

     安全金鑰使用新的安全金鑰 PIN 更新，以便與您的工作或學校帳戶一起使用。 如果您決定再次更改 PIN，則可以選擇 **"更改**"按鈕。
4. 選擇 **"關閉**"以關閉 **"管理"** 螢幕。

## <a name="additional-security-info-methods"></a>其他安全性資訊方法

要註冊安全金鑰，必須至少註冊一個附加的安全驗證方法。 有關詳細資訊，請參閱[概述部分](security-info-add-update-methods-overview.md)。 

## <a name="next-steps"></a>後續步驟

- 有關無密碼驗證方法的詳細資訊，請閱讀 Microsoft[的 Azure AD 開始公開預覽 FIDO2 安全金鑰，啟用無密碼登錄](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins)博客，或閱讀[什麼是 Microsoft 身份驗證器應用？](user-help-auth-app-overview.md) [Windows Hello overview](https://www.microsoft.com/windows/windows-hello)

- 有關符合 Microsoft[的安全金鑰](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)的詳細資訊。

- 如果您遺失或忘記密碼，請從[密碼重設入口網站](https://passwordreset.microsoftonline.com/)或遵循[重設公司或學校密碼](active-directory-passwords-update-your-own-password.md)一文中的步驟來重設密碼。

- 在[無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中取得登入問題的疑難排解秘訣和說明。
