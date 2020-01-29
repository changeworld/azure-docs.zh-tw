---
title: 快速入門 - Azure AD 自助式密碼重設
description: 在本快速入門中，您將了解如何設定 Azure AD 自助式密碼重設，以允許使用者重設自己的密碼來降低 IT 部門的負擔。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd178f52665c77f03a48d87a9e73c9019390bb21
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154851"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>快速入門：設定 Azure Active Directory 自助式密碼重設

在本快速入門中，您將設定 Azure Active Directory (AD) 自助式密碼重設 (SSPR)，讓使用者可重設密碼或解除鎖定帳戶。 透過 SSPR，使用者可以重設自己的認證，而不需要技術服務人員或系統管理員的協助。 此功能可讓使用者重新取得其帳戶的存取權，而不需要等待額外的支援。

> [!IMPORTANT]
> 本快速入門將說明系統管理員如何啟用自助式密碼重設。 如果您是已註冊自助式密碼重設的使用者，而且需要取回您的帳戶，請移至 https://aka.ms/sspr 。
>
> 如果您的 IT 小組尚未啟用重設您密碼的功能，請與您的技術服務人員聯繫以取得其他協助。

## <a name="prerequisites"></a>Prerequisites

* 可運作且至少已啟用試用版授權的 Azure AD 租用戶。
    * 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 具有全域系統管理員權限的帳戶。
* 具有已知密碼的非系統管理員測試使用者，例如 testuser  。
    * 如果您需要建立使用者，請參閱 [快速入門：將使用者新增至 Azure Active Directory](../add-users-azure-active-directory.md) 中的資訊。
* 要用來測試非系統管理員測試使用者是否為其成員的試驗群組，例如 SSPR-Test-Group  。
    * 如果您需要建立群組，請參閱如何[在 Azure Active Directory 中建立群組和新增成員](../active-directory-groups-create-azure-portal.md)。

## <a name="enable-self-service-password-reset"></a>啟用自助式密碼重設

[在 YouTube 上以影片形式觀看此程序](https://youtu.be/Pa0eyqjEjvQ)

1. 從 Azure 入口網站功能表或 [首頁]  頁面，選取 [Azure Active Directory]  ，然後選擇 [密碼重設]  。

1. 在 [屬性]  頁面的 [已啟用自助式密碼重設]  選項下方，選擇 [已選取]  。
1. 選擇 [選取群組]  ，然後選取您在本文必要條件一節中建立的試驗群組，例如 SSPR-Test-Group  。 在準備就緒時，選取 [儲存]  。
1. 在 [驗證方法]  頁面中選擇下列項目，然後選擇 [儲存]  ：
    * 需要重設的方法數：**1**
    * 使用者可用方法：
        * **行動應用程式程式碼**
        * **電子郵件**

    > [!div class="mx-imgBorder"]
    > ![選擇 SSPR 的驗證方法][Authentication]

4. 在 [註冊]  頁面中選擇下列項目，然後選擇 [儲存]  ：
   * 要求使用者在登入時註冊：**是**
   * 設定要求使用者重新確認其驗證資訊的等候天數：**365**

## <a name="test-self-service-password-reset"></a>測試自助式密碼重設

現在，我們將使用上一節中所選群組的測試使用者來測試您的 SSPR 設定，例如 testuser  。 由於 Microsoft 會強制執行 Azure 系統管理員帳戶的強式驗證需求，使用系統管理員帳戶的測試可能會有不同的結果。 如需有關系統管理員密碼原則的詳細資訊，請參閱我們的[密碼原則](concept-sspr-policy.md)一文。

1. 在 InPrivate 或 Incognito 模式中開啟新的瀏覽器視窗，並瀏覽至 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)。
2. 以非系統管理員測試使用者的身分登入 (例如 testuser  )，並註冊您的驗證電話。
3. 完成後，選取標示為**狀況良好**的按鈕，然後關閉瀏覽器視窗。
4. 在 InPrivate 或 Incognito 模式中開啟新的瀏覽器視窗，並瀏覽至 [https://aka.ms/sspr](https://aka.ms/sspr)。
5. 輸入非系統管理員測試使用者的使用者識別碼 (例如 testuser  )，以及 CAPTCHA 中的字元，然後選取 [下一步]  。
6. 依照驗證步驟重設您的密碼。

## <a name="clean-up-resources"></a>清除資源

若要停用自助式密碼重設，請在 Azure 入口網站中搜尋並選取 **Azure Active Directory**。 選取 [密碼重設]  ，然後選擇 [已啟用自助式密碼重設]  下方的 [無]  。 在準備就緒時，選取 [儲存]  。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何為僅限雲端的使用者設定自助式密碼重設。 若要了解如何完成更詳盡的推出，請繼續進行我們的推出指南。

> [!div class="nextstepaction"]
> [推出自助式密碼重設](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "可供使用的 Azure AD 驗證方法和所需的數量"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
