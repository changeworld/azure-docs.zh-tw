---
title: 變更您的雙因素驗證方法和設定-Azure AD
description: 瞭解如何從 [其他安全性驗證] 頁面，變更您的公司或學校帳戶的安全性驗證方法和設定。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: lizross
ms.openlocfilehash: e48834800f1fe3d34c13a87cfb3d8ef00c9e6bff
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705270"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>變更您的雙因素驗證方法和設定

設定公司或學校帳戶的安全性驗證方法之後，您可以更新任何相關的詳細資料，包括：

- 選擇預設的安全性驗證方法。

- 新增或更新您的安全性驗證方法詳細資料，例如您的電話號碼。

- 設定新的驗證器應用程式，或從驗證器應用程式刪除裝置。

## <a name="using-the-additional-security-verification-page"></a>使用 [其他安全性驗證] 頁面

如果您的組織提供如何開啟和管理雙因素驗證的特定步驟，您應該遵循這些指示。 否則，您可以從 [[其他安全性驗證](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)] 頁面取得您的安全性驗證方法設定。

>[!Note]
>如果您在畫面上看到的內容不符合本文所涵蓋的內容，則表示您的系統管理員已開啟安全性資訊（預覽）體驗，或您的組織有自己的自訂入口網站。 如需安全性資訊體驗的詳細資訊，請參閱[安全性資訊（預覽）總覽](user-help-security-info-overview.md)。 如需貴組織自訂入口網站的詳細資訊，您必須聯絡支援人員。

### <a name="to-get-to-the-additional-security-verification-page"></a>若要取得其他安全性驗證頁面

- 移至 [[其他安全性驗證](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)] 頁面。

    ![[其他安全性驗證] 頁面，其中包含可用的安全性驗證方法詳細資料](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

    如果按一下連結並沒有發生作用，您也可以遵循下列步驟前往 [其他安全性驗證] 頁面：

    1. 登入 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

    2. 在右上方選取您的帳戶名稱，然後選取 [設定檔]。

    3. 選取 [其他安全性驗證]。  

        ![[其他安全性驗證] 頁面的我的應用程式連結](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>如需有關使用 [**其他安全性驗證**] 頁面的 [**應用程式密碼**] 區段的詳細資訊，請參閱[管理雙因素驗證的應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)。 應用程式密碼只能用於尚未支援雙因素驗證的應用程式。

## <a name="change-your-default-security-verification-method"></a>變更預設的安全性驗證方法

使用您的使用者名稱和密碼登入您的公司或學校帳戶之後，您將會自動看到您選擇的安全性驗證方法。 視組織的需求而定，這可能是透過驗證器應用程式、文字訊息或通話的通知或驗證碼。

如果您決定要變更您所使用的預設安全性驗證方法，可以從這裡執行。

### <a name="to-change-your-default-security-verification-method"></a>變更預設的安全性驗證方法

1. 從 [**其他安全性驗證**] 頁面中，從 [**您偏好的選項**] 下拉式清單中選取要使用的方法。 您將會看到所有選項，但您只能選擇您的組織可使用的選項。

    - **透過代理程式更新我。** 您會透過驗證器代理程式更新您有等待驗證提示。

    - **撥打我的驗證電話。** 您會在行動裝置上取得電話，要求您驗證您的資訊。

    - **驗證電話的文字代碼。** 您會在行動裝置上取得驗證碼作為文字訊息的一部分。 您必須在公司或學校帳戶的驗證提示中輸入此程式碼。

    - **撥打我的辦公室電話。** 您會在您的辦公室電話上撥打電話，要求您驗證您的資訊。

    - **使用來自應用程式的驗證碼。** 您將使用您的驗證器應用程式取得驗證碼，您會在公司或學校帳戶的提示中輸入。

2. 選取 [儲存]。

## <a name="add-or-change-your-phone-number"></a>新增或變更您的電話號碼

您可以從 [**其他安全性驗證**] 頁面新增電話號碼，或更新現有的號碼。

>[!Important]
>強烈建議您新增次要電話號碼，以在主要電話遺失或遭竊時防止鎖定您的帳戶，或者，如果您收到新的電話，且不再有原始的主要電話號碼。

### <a name="to-change-your-phone-numbers"></a>變更您的電話號碼

1. 在 [**其他安全性驗證**] 頁面的 [**您要如何回應？** ] 區段中，更新**驗證電話**（您的主要行動裝置）和**辦公室電話**的電話號碼資訊。

2. 選取 [**備用驗證電話**] 選項旁的方塊，然後輸入次要電話號碼，如果您無法存取主要裝置，就可以在其中接收文字訊息或來電。

3. 選取 [儲存]。

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>將新帳戶新增至 Microsoft 驗證器應用程式

您可以在適用于[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)或[iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)的 Microsoft Authenticator 應用程式上設定工作或學校帳戶。

如果您先前已在 Microsoft Authenticator 應用程式中設定您的公司或學校帳戶，則不需要再次執行。

1. 在 [**其他安全性驗證**] 頁面的 [**您要如何回應？** ] 區段中，選取 [**設定驗證器應用程式**] 按鈕。

    ![在 Microsoft Authenticator 應用程式中設定您的公司或學校帳戶](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

2. 遵循畫面上的指示，包括使用您的行動裝置掃描 QR 代碼，然後選取 **[下一步]** 。

    系統會要求您透過 Microsoft Authenticator 應用程式核准通知，以驗證您的資訊。

3. 選取 [儲存]。

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>從 Microsoft Authenticator 應用程式中刪除您的帳戶或裝置

您可以從 Microsoft Authenticator 應用程式中刪除您的帳戶，也可以從工作或學校帳戶刪除您的裝置。 通常您會刪除裝置，以從您的帳戶永久移除遺失、遭竊或舊的裝置，並刪除您的帳戶以嘗試修正某些連線問題或處理帳戶變更，例如新的使用者名稱。

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>從您的公司或學校帳戶刪除您的裝置

1. 在 [**其他安全性驗證**] 頁面的 [**您要如何回應？** ] 區段中，選取 [**設定驗證器應用程式**] 按鈕。

2. 選取 [儲存]。

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>從 Microsoft Authenticator 應用程式刪除您的帳戶

- 從 Microsoft Authenticator 應用程式中，選取您要刪除之裝置旁的 [**刪除**] 按鈕。

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>在受信任的裝置上開啟雙因素驗證提示

視您的組織設定而定，您可能會看到一個核取方塊，指出當您在瀏覽器上執行雙因素驗證時，**不會再詢問 X 天**。 如果您已核取此方塊以停止雙因素驗證提示，然後遺失您的裝置或裝置可能遭到入侵，您應該重新開啟雙因素驗證提示，以協助保護您的帳戶。 可惜的是，您無法針對單一裝置重新開啟提示。 您必須同時開啟所有裝置的提示。

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>若要針對您的裝置重新開啟雙因素驗證提示

- 從 [**其他安全性驗證**] 頁面中，選取 [**在先前信任的裝置上還原多重要素驗證**]。

    下次您登入任何裝置時，系統會提示您執行雙因素驗證。

## <a name="next-steps"></a>後續步驟

新增或更新您的雙因素驗證設定之後，您可以管理您的應用程式密碼、登入，或取得一些常見的雙因素驗證相關問題的協助。

- 針對不支援雙因素驗證的任何應用程式，[管理雙因素驗證的應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)。

- [使用雙因素驗證來登入](multi-factor-authentication-end-user-signin.md)

- [取得雙因素驗證的協助](multi-factor-authentication-end-user-troubleshoot.md)
