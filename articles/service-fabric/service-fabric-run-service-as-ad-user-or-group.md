---
title: 以 AD 使用者或群組的形式執行 Azure Service Fabric 服務
description: 了解如何以 Active Directory 使用者或群組身分在 Service Fabric Windows 獨立叢集上執行 Service Fabric。
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: d4a7afc2ddb0f39014a7cf0fd006d7fe23673a95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840722"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>以 Active Directory 使用者或群組身分執行服務
在 Windows Server 獨立叢集上，您可以使用 RunAs 原則，以 Active Directory 使用者或群組身分執行服務。  根據預設，Service Fabric 應用程式會在用以執行 Fabric.exe 程序的帳戶之下執行。 即使在共用主控環境中，以不同帳戶執行應用程式能避免彼此干擾。 請注意，這會使用網域內部部署的 Active Directory，不是 Azure Active Directory (Azure AD)。  您也可以利用[群組受控服務帳戶 (gMSA)](service-fabric-run-service-as-gmsa.md) 身分執行服務。

然後，您可以使用網域使用者或群組，存取網域中已經被授與權限的其他資源 (例如檔案共用)。

下列範例顯示的 Active Directory 使用者稱為 *TestUser*，其網域密碼以稱為 *MyCert* 的憑證加密。 您可以使用 `Invoke-ServiceFabricEncryptText` Powershell 命令來建立密碼加密文字。 如需詳細資訊，請參閱[管理 Service Fabric 應用程式中的密碼](service-fabric-application-secret-management.md)。

您必須將密碼解密的憑證私密金鑰以頻外方法部署到本機電腦 (在 Azure 中，這是透過 Azure Resource Manager)。 然後，當 Service Fabric 將服務封裝部署到電腦時，它就能夠將密碼解密，並連同使用者名稱向 Active Directory 進行驗證，而在這些認證下執行。

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> 如果您將 RunAs 原則套用到服務，而服務資訊清單宣告具有 HTTP 通訊協定的端點資源，還必須指定 **SecurityAccessPolicy**。  如需詳細資訊，請參閱[為 HTTP 和 HTTPS 端點指派安全性存取原則](service-fabric-assign-policy-to-endpoint.md)。 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
若要了解下一個步驟，請閱讀下列文章：
* [了解應用程式模型](service-fabric-application-model.md)
* [在服務資訊清單中指定資源](service-fabric-service-manifest-resources.md)
* [部署應用程式](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
