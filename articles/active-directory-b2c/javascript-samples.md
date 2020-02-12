---
title: JavaScript 範例
titleSuffix: Azure AD B2C
description: 了解在 Azure Active Directory B2C 中使用 JavaScript。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1381ddb16697b1e892794604bbfafda815bd6182
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149063"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>適用於 Azure Active Directory B2C 中的 JavaScript 範例

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

您可以將自己的 JavaScript 用戶端程式代碼新增至您的 Azure Active Directory B2C （Azure AD B2C）應用程式。

若要為您的應用程式啟用 JavaScript：

* 將元素新增至您的[自訂原則](custom-policy-overview.md)
* 選取[頁面配置](page-layout.md)
* 在您的要求中使用[b2clogin.com](b2clogin.md)

本文說明如何變更自訂原則，以啟用腳本執行。

> [!NOTE]
> 如果您想要為使用者流程啟用 JavaScript，請參閱[Azure Active Directory B2C 中的 javascript 和頁面配置版本](user-flow-javascript-overview.md)。

## <a name="prerequisites"></a>必要條件

### <a name="select-a-page-layout"></a>選取頁面配置

* 為應用程式的使用者介面元素選取[頁面配置](contentdefinitions.md#select-a-page-layout)。

    如果您想要使用 JavaScript，您必須針對自訂原則中的*所有*內容定義，使用頁面 `contract` 版本[定義頁面配置版本](contentdefinitions.md#migrating-to-page-layout)。

## <a name="add-the-scriptexecution-element"></a>新增 ScriptExecution 元素

您必須將 **ScriptExecution** 元素新增到 [RelyingParty](relyingparty.md) 元素來啟用指令碼執行。

1. 開啟您的自訂原則檔案。 例如 *SignUpOrSignin.xml*。
2. 將 **ScriptExecution** 元素新增到 **RelyingParty** 的 **UserJourneyBehaviors** 元素：

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. 儲存並上傳該檔案。

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

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

在程式碼中，請將 `termsOfUseUrl` 取代為您使用規定合約的連結。 針對您的目錄，建立名為**termsOfUse**的新使用者屬性，然後將**termsOfUse**納入為使用者屬性。

## <a name="next-steps"></a>後續步驟

如需如何自訂應用程式之使用者介面的詳細資訊，請參閱[在 Azure Active Directory B2C 中使用自訂原則來自訂應用程式的使用者介面](custom-policy-ui-customization.md)。
