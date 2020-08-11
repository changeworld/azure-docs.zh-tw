---
title: 以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器 | Microsoft Docs
description: 涵蓋如何執行自動安裝 Azure AD 應用程式 Proxy 連接器，為內部部署的應用程式提供安全的遠端存取。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5fc1b84e624828d7feb64bd53e8fe8ffff2a7ff
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054829"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>建立 Azure AD 應用程式 Proxy 連接器的自動安裝指令碼

本主題會協助您建立 Windows PowerShell 指令碼，以便自動安裝和註冊 Azure AD 應用程式 Proxy 連接器。

當您想要執行下列工作時，此功能很實用︰

* 在未啟用使用者介面或您無法使用遠端桌面存取的 Windows 伺服器上安裝連接器。
* 一次安裝並註冊許多連接器。
* 將連接器安裝與註冊整合成另一個程序的一部分。
* 建立一個包含連接器位元但未註冊的標準伺服器映像。

若要讓[應用程式 Proxy 連接器](application-proxy-connectors.md)能夠正常執行，必須使用應用程式系統管理員和密碼向您的 Azure AD 目錄註冊。 通常，此資訊是在連接器安裝期間於一個快顯對話方塊中輸入的，但是您可以改用 PowerShell 使此程序自動進行。

自動安裝有兩個步驟。 首先，安裝連接器。 其次，向 Azure AD 註冊連接器。

> [!IMPORTANT]
> 如果您要安裝適用于 Azure Government cloud 的[連接器，請](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#allow-access-to-urls)參閱必要條件和[安裝步驟](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#install-the-agent-for-the-azure-government-cloud)。 您需要啟用一組不同 URL 的存取權，以及執行安裝的其他參數。

## <a name="install-the-connector"></a>安裝連接器
使用下列步驟安裝連接器而不註冊連接器：

1. 開啟命令提示字元。
2. 執行下列命令，/q 代表無訊息安裝。 無訊息安裝不會提示您接受《使用者授權合約》。

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>向 Azure AD 註冊連接器
有兩種方法可用來註冊連接器︰

* 使用 Windows PowerShell 認證物件註冊連接器
* 使用離線時建立的權杖註冊連接器

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>使用 Windows PowerShell 認證物件註冊連接器
1. 建立 Windows PowerShell 認證物件 `$cred`，其中含有目錄的系統管理使用者名稱和密碼。 執行下列命令，並取代 *\<username\>* 和 *\<password\>* ：

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. 移至 **C:\Program Files\Microsoft AAD App Proxy Connector**，然後使用您建立的 `$cred` 物件來執行下列指令碼：

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>使用離線時建立的權杖註冊連接器
1. 使用此程式碼片段中的值或下列 PowerShell Cmdlet，建立使用 AuthenticationContext 類別的離線權杖：

   **使用 c #：**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **使用 PowerShell：**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. 建立權杖後，請使用該權杖建立一個 SecureString：

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. 執行下列 Windows PowerShell 命令， \<tenant GUID\> 並將取代為您的目錄識別碼：

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>後續步驟
* [使用您自己的網域名稱發行應用程式](application-proxy-configure-custom-domain.md)
* [啟用單一登入](application-proxy-configure-single-sign-on-with-kcd.md)
* [使用應用程式 Proxy 疑難排解您遇到的問題](application-proxy-troubleshoot.md)
