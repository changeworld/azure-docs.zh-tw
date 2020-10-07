---
title: 搭配 MSAL Android 使用共用裝置模式 | Azure
description: 了解如何準備可在共用模式中執行的 Android 裝置，並執行第一線工作角色應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4bbcf73654d7f588c63a9bf81ab6a689360ec978
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91355052"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>教學課程：在 Android 應用程式中使用共用裝置模式

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="developer-guide"></a>開發人員指南

本指南提供開發人員指導方針，說明如何使用 Microsoft 驗證程式庫 (MSAL) 在 Android 應用程式中執行共用裝置模式。 請參閱 [MSAL Android 教學課程](./tutorial-v2-android.md)，以了解如何整合 MSAL 與 Android 應用程式、登入使用者、呼叫 Microsoft Graph 及登出使用者。

### <a name="download-the-sample"></a>下載範例

從 GitHub 複製[範例應用程式](https://github.com/Azure-Samples/ms-identity-android-java/)。 此範例可在[單一或多個帳戶模式](./single-multi-account.md)中使用。

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>將 MSAL SDK 新增至您的本機 Maven 存放庫

如果您不是使用範例應用程式，請將 MSAL 程式庫新增為 build.gradle 檔案中的相依性，如下所示：

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>將您的應用程式設定為使用共用裝置模式

如需有關設定組態檔的詳細資訊，請參閱[設定文件](./msal-configuration.md)。

在 MSAL 組態檔中，將 `"shared_device_mode_supported"` 設定為 `true`。

您可能並沒有打算支援多帳戶模式。 此情況可能是您並非使用共用裝置，而且使用者可以同時使用一個以上的帳戶登入應用程式。 若是如此，請將 `"account_mode"` 設定為 `"SINGLE"`。 這可保證您的應用程式一律會取得 `ISingleAccountPublicClientApplication`，並大幅簡化您的 MSAL 整合。 `"account_mode"` 的預設值是 `"MULTIPLE"`，因此如果您使用 `"single account"` 模式，請務必在組態檔中變更此值。

以下是 auth_config.json 檔案的範例，包含在範例應用程式的 **app**>**main**>**res**>**raw** 目錄中：

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>偵測共用裝置模式

共用裝置模式可讓您將 Android 裝置設定為可由多個員工共用，同時為裝置提供 Microsoft 身分識別支援的管理方式。 員工可以登入他們的裝置，並快速存取客戶資訊。 當他們完成其變更或工作時，只要按一下就能登出共用裝置上的所有應用程式，而裝置可立即供下一位員工使用。

使用 `isSharedDevice()` 判斷應用程式是否在使用共用裝置模式的裝置上執行。 您的應用程式可以使用此旗標來判斷是否應該據此修改 UX。

以下是說明如何使用 `isSharedDevice()` 的程式碼片段。  其來自範例應用程式中的 `SingleAccountModeFragment` 類別：

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>初始化 PublicClientApplication 物件

如果您在 MSAL 組態檔中設定 `"account_mode":"SINGLE"`，您可以將傳回的應用程式物件安全地轉換為 `ISingleAccountPublicCLientApplication`。

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>偵測單一與多個帳戶模式

如果您撰寫的應用程式只會用於共用裝置上的第一線工作角色，建議您將應用程式撰寫為只支援單一帳戶模式。 這包括大部分以工作為主的應用程式，例如醫療記錄應用程式、發票應用程式，以及大多數的企業營運應用程式。 這可簡化您的開發，因為不需要容納 SDK 的許多功能。

如果您的應用程式支援多個帳戶及共用裝置模式，您必須執行類型檢查並轉換成適當的介面，如下所示。

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>取得已登入的使用者，並判斷裝置上的使用者是否已變更

`loadAccount` 方法會擷取登入使用者的帳戶。 `onAccountChanged` 方法會判斷登入的使用者是否已變更，如果是，則執行清除作業：

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>全域登入使用者

下列動作會將裝置上的使用者登入搭配使用 MSAL 和 Authenticator 應用程式的其他應用程式：

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>全域登出使用者

下列動作會移除已登入的帳戶，並從應用程式和使用共用裝置模式的裝置中清除快取權杖：

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>系統管理員指南

下列步驟說明如何在 Azure 入口網站中設定應用程式，以及如何讓裝置處於共用裝置模式。

### <a name="register-your-application-in-azure-active-directory"></a>在 Azure Active Directory 中註冊您的應用程式

首先，在您的組織租用戶中註冊您的應用程式。 然後依序在 auth_config.json 中提供下列值，好讓應用程式可正確執行。

如需如何執行此動作的相關資訊，請參閱[註冊應用程式](./tutorial-v2-android.md#register-your-application)。

> [!NOTE]
> 當您註冊應用程式時，請使用左側的快速入門指南，然後選取 [Android]  。 這會引導您到一個頁面，然後要求您提供應用程式的**套件名稱**及**簽章雜湊**。 這些是確保您應用程式組態能夠正常執行的重要項目。 接著，您會收到用於應用程式的組態物件，您可以將其剪下並貼到 auth_config.json 檔案中。

![應用程式註冊畫面](media/tutorial-v2-shared-device-mode/register-app.png) 您應選取 [為我進行這項變更]  ，然後提供快速入門在 Azure 入口網站中要求的值。 完成後，我們將會產生您所需的所有組態檔。

![應用程式組態資訊畫面](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>設定租用戶

基於測試目的，請在您的租用戶者中設定下列各項：至少兩位員工，一個雲端裝置管理員和一個全域管理員。 在 Azure 入口網站中，藉由修改組織角色來設定雲端裝置管理員。 在 Azure 入口網站中，您可以選取 [Azure Active Directory]   > [角色和管理員]   > [雲端裝置管理員]  來存取您的組織角色。 新增可讓裝置處於共用模式的使用者。

## <a name="set-up-an-android-device-in-shared-mode"></a>在共用模式中設定 Android 裝置

### <a name="download-the-authenticator-app"></a>下載 Authenticator 應用程式

從 Google Play 商店下載 Microsoft Authenticator 應用程式。 如果您已下載應用程式，請確定您使用的是最新版本。

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Authenticator 應用程式設定與在雲端中註冊裝置

啟動 Authenticator 應用程式，並瀏覽至主要帳戶頁面。 一旦您看到 [新增帳戶]  頁面，表示您可以準備讓裝置進行共用。

![Authenticator 的新增帳戶畫面](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 使用右側功能表列移至 [設定]  窗格。 選取 [公司與學校帳戶]  底下的 [裝置註冊]  。

 ![Authenticator 的新增帳戶畫面](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 當您按一下此按鈕時，系統會要求您授與裝置連絡人的存取權。 這是因為要整合裝置上的 Android 帳戶。 請選擇 [允許]  。

 ![Authenticator 的新增帳戶畫面](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

雲端裝置管理員應該在 [或註冊為共用裝置]  中輸入其組織電子郵件。 接著按一下 [註冊為共用裝置]  按鈕，然後輸入其認證。

![註冊裝置畫面](media/tutorial-v2-shared-device-mode/register-device.png)

![此螢幕擷取畫面顯示登入頁面。](media/tutorial-v2-shared-device-mode/sign-in.png)

裝置現在已處於共用模式。

![註冊裝置畫面](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 裝置上的任何登入和登出都是全域性的，這表示這些動作會套用到裝置上與 MSAL 和 Microsoft Authenticator 整合的所有應用程式。 您現在可以將應用程式部署到使用共用裝置模式功能的裝置。

## <a name="view-the-shared-device-in-the-azure-portal"></a>在 Azure 入口網站中查看共用裝置

一旦將裝置置於共用模式後，您的組織就會認識該裝置，並在您的組織租用戶中進行追蹤。 若要檢視您的共用裝置，您可以在Azure 入口網站的 [Azure Active Directory] 分頁中查看 [聯結類型]  。

![Azure 入口網站中的所有裝置刀鋒視窗](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>執行範例應用程式

範例應用程式是簡單的應用程式，可用來呼叫您組織的圖形 API。 第一次執行時，系統會顯示要求同意的提示，因為對您的員工帳戶而言，這是新的應用程式。

![應用程式組態資訊畫面](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>後續步驟

在[適用於 Android 裝置的共用裝置模式](msal-android-shared-devices.md)中深入了解共用模式