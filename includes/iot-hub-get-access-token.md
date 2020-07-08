---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "70803001"
---
## <a name="obtain-an-azure-resource-manager-token"></a>取得 Azure Resource Manager 權杖
Azure Active Directory 必須驗證您在使用 Azure 資源管理員的資源上執行的所有工作。 此處顯示的範例使用密碼驗證，如需其他方法，請參閱[驗證 Azure Resource Manager 要求][lnk-authenticate-arm]。

1. 將下列程式碼加入 Program.cs 中的 **Main** 方法，以使用應用程式識別碼和密碼從 Azure AD 擷取權杖。
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. 將下列程式碼新增至 **Main** 方法的結尾，建立使用該權杖的 **ResourceManagementClient** 物件：
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. 建立或取得您正在使用的資源群組參照：
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx