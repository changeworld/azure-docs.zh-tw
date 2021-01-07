---
title: JavaScript 和頁面配置版本
titleSuffix: Azure AD B2C
description: 瞭解如何啟用 JavaScript 並使用 Azure Active Directory B2C 中的頁面配置版本。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1a1fa09c7c9b8baeeb8f3c2c9b2cf8271e513be1
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963531"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 JavaScript 和頁面配置版本

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C 針對使用者流程和自訂原則中的使用者介面元素，提供一組包含 HTML、CSS 和 JavaScript 的封裝內容。 若要為您的應用程式啟用 JavaScript：

::: zone pivot="b2c-user-flow"

* 選取 [頁面配置](page-layout.md)
* 使用 Azure 入口網站在使用者流程上啟用它
* 在您的要求中使用[b2clogin.com](b2clogin.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

* 選取 [頁面配置](page-layout.md)
* 將元素新增至您的 [自訂原則](custom-policy-overview.md)
* 在您的要求中使用[b2clogin.com](b2clogin.md)

::: zone-end

## <a name="prerequisites"></a>先決條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>選取頁面配置版本

如果您想要啟用 JavaScript 用戶端程式代碼，您的 JavaScript 所依據的元素必須是不可變的。 如果它們不是不可變的，則任何變更可能會在使用者頁面上造成非預期的行為。 若要避免這些問題，請強制使用頁面配置並指定頁面配置版本，以確保您以 JavaScript 為基礎的內容定義是不可變的。 即使您不打算啟用 JavaScript，也可以指定頁面的頁面配置版本。

::: zone pivot="b2c-user-flow"

若要指定使用者流程頁面的頁面配置版本： 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 選取您的原則 (例如「B2C_1_SignupSignin」) 以開啟之。
1. 選取 [ **頁面配置**]。 在 [配置 **名稱**] 下，選取 [使用者流程] 頁面，然後選擇 [ **版面配置版本 (預覽])**。

如需不同頁面配置版本的詳細資訊，請參閱 [頁面配置版本變更記錄](page-layout.md)檔。

![入口網站中顯示頁面配置版本下拉式清單的頁面配置設定](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

選取應用程式使用者介面元素的 [頁面配置](contentdefinitions.md#select-a-page-layout) 。

[](contentdefinitions.md#migrating-to-page-layout) `contract` 針對您自訂原則中的 *所有* 內容定義，定義頁面版本的頁面配置版本。 值的格式必須包含 " `contract` _urn： com： microsoft： aad： b2c： elements：**contract**:p age： version_。 瞭解如何使用頁面版本 [遷移至頁面配置](contentdefinitions.md#migrating-to-page-layout) 。

下列範例會顯示內容定義識別碼和對應的 **DataUri** 與頁面合約： 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>啟用 JavaScript

::: zone pivot="b2c-user-flow"

在使用者流程 **屬性** 中，您可以啟用 JavaScript。 啟用 JavaScript 也會強制使用頁面配置。 接著，您可以設定使用者流程的頁面配置版本，如下一節所述。

![已反白顯示 [啟用 JavaScript] 設定的 [使用者流程屬性] 頁面](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

您必須將 **ScriptExecution** 元素新增到 [RelyingParty](relyingparty.md) 元素來啟用指令碼執行。

1. 開啟您的自訂原則檔案。 例如 *SignUpOrSignin.xml*。
1. 將 **ScriptExecution** 元素加入至 **RelyingParty** 元素：

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. 儲存並上傳該檔案。

::: zone-end

## <a name="guidelines-for-using-javascript"></a>使用 JavaScript 的指導方針

在您使用 JavaScript 來自訂應用程式的介面時，請遵循這些指導方針：

- 不要將 Click 事件繫結到 `<a>` HTML 元素上。
- 不要採用 Azure AD B2C 程式碼或註解上的相依性。
- 不要變更 Azure AD B2C HTML 元素的順序或階層。 使用 Azure AD B2C 原則來控制 UI 元素的順序。
- 您可以呼叫任何 RESTful 服務，但有下列考量：
    - 您可能需要將 RESTful 服務 CORS 設定為允許用戶端 HTTP 呼叫。
    - 確定您的 RESTful 服務是安全的，並僅使用 HTTPS 通訊協定。
    - 不要直接使用 JavaScript 來呼叫 Azure AD B2C 端點。
- 您可以內嵌 JavaScript 或連結至外部的 JavaScript 檔案。 使用外部 JavaScript 檔案時，請務必使用絕對 URL，而非相對 URL。
- JavaScript 架構：
    - Azure AD B2C 會使用特定版本的 jQuery。 不要包含其他版本的 jQuery。 在相同頁面上使用多個版本會導致問題。
    - 不支援使用 RequireJS。
    - Azure AD B2C 不支援大部分的 JavaScript 架構。
- 可以呼叫 `window.SETTINGS`、`window.CONTENT` 物件來讀取 Azure AD B2C 設定，例如目前的 UI 語言。 請不要變更這些物件的值。
- 若要自訂 Azure AD B2C 錯誤訊息，請在原則中使用當地語系化。
- 如果可以透過原則來達成某個事項，便建議使用原則來達成它。

## <a name="javascript-samples"></a>JavaScript 範例

### <a name="show-or-hide-a-password"></a>顯示或隱藏密碼

協助客戶成功註冊的常見方式，便是允許他們查看自己所輸入的密碼。 此選項可讓使用者輕鬆地查看其密碼，並視需要做出更正，以協助他們完成註冊。 任何輸入密碼的欄位都會有具有 [顯示密碼] 標籤的核取方塊。  這可讓使用者以純文字的形式查看密碼。 將此程式碼片段包含到您適用於自我判斷頁面的註冊或登入範本：

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>新增使用規定

將下列程式碼包含到您在自己的頁面中想包含 [使用規定] 核取方塊的位置。 在您的本機帳戶註冊和社交帳戶註冊頁面中，通常會需要這個核取方塊。

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

在程式碼中，請將 `termsOfUseUrl` 取代為您使用規定合約的連結。 針對您的目錄，建立名為 **>termsofuse** 的新使用者屬性，然後將 **>termsofuse** 包含為使用者屬性。

## <a name="next-steps"></a>後續步驟

在 Azure Active Directory B2C 中，您可以從 [自訂應用程式的使用者介面](customize-ui-with-html.md)，以取得如何自訂應用程式使用者介面的詳細資訊。
