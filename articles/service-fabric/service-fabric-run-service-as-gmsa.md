---
title: 在 gMSA 帳戶下執行 Azure Service Fabric 服務
description: 瞭解如何在 Service Fabric 的 Windows 獨立叢集上，以群組管理的服務帳戶（gMSA）執行服務。
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76988391"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>以群組受控服務帳戶身分執行服務

在 Windows Server 獨立叢集上，您可以使用*RunAs*原則，以*群組受管理的服務帳戶*（gMSA）來執行服務。  根據預設，Service Fabric 應用程式會在執行進程的帳戶下執行 `Fabric.exe` 。 即使在共用主控環境中，以不同帳戶執行應用程式能避免彼此干擾。 使用 gMSA，就不需將密碼或加密的密碼儲存於應用程式資訊清單中。  您也可以利用 [Active Directory 使用者或群組](service-fabric-run-service-as-ad-user-or-group.md)身分執行服務。

下列範例顯示如何建立名為*svc-Test $* 的 gMSA 帳戶、如何將該受管理的服務帳戶部署至叢集節點，以及如何設定使用者主體。

> [!NOTE]
> 使用 gMSA 搭配獨立的 Service Fabric 叢集需要在您的網域內 Active Directory 內部部署（而不是 Azure Active Directory （Azure AD））。

先決條件：

- 網域需要一個 KDS 根金鑰。
- 網域中必須至少有一個 Windows Server 2012 （或 R2） DC。

1. 讓 Active Directory 網域系統管理員使用 Cmdlet 建立群組管理的服務帳戶 `New-ADServiceAccount` ，並確定 `PrincipalsAllowedToRetrieveManagedPassword` 包含所有的 Service Fabric 叢集節點。 `AccountName`、`DnsHostName` 和 `ServicePrincipalName` 必須是唯一的。

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. 在每個 Service Fabric 叢集節點 (例如 `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`) 上，安裝並測試 gMSA。
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. 設定使用者主體，並設定 `RunAsPolicy` 來參考[使用者](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas)。
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE]
> 如果您將 RunAs 原則套用到服務，而服務資訊清單宣告具有 HTTP 通訊協定的端點資源，您就必須指定 **SecurityAccessPolicy**。  如需詳細資訊，請參閱[為 HTTP 和 HTTPS 端點指派安全性存取原則](service-fabric-assign-policy-to-endpoint.md)。
>

下列文章將引導您完成接下來的步驟：

- [了解應用程式模型](service-fabric-application-model.md)
- [在服務資訊清單中指定資源](service-fabric-service-manifest-resources.md)
- [部署應用程式](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
