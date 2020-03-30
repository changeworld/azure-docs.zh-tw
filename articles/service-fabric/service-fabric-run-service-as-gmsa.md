---
title: 在 gMSA 帳戶下運行 Azure 服務交換矩陣服務
description: 瞭解如何在服務結構 Windows 獨立群集上以組託管服務帳戶 （gMSA） 方式運行服務。
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988391"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>以群組受控服務帳戶身分執行服務

在 Windows Server 獨立群集上，可以使用*RunAs*策略將服務作為*組託管服務帳戶*（gMSA） 運行。  預設情況下，Service Fabric 應用程式在`Fabric.exe`進程運行的帳戶下運行。 即使在共用主控環境中，以不同帳戶執行應用程式能避免彼此干擾。 使用 gMSA，就不需將密碼或加密的密碼儲存於應用程式資訊清單中。  您也可以利用 [Active Directory 使用者或群組](service-fabric-run-service-as-ad-user-or-group.md)身分執行服務。

下面的示例演示如何創建名為*svc-Test$* 的 gMSA 帳戶，如何將託管服務帳戶部署到叢集節點，以及如何配置使用者主體。

> [!NOTE]
> 將 gMSA 與獨立的 Service Fabric 群集一起使用需要在域內本地（而不是 Azure 活動目錄 （Azure AD））內本地使用活動目錄。

先決條件：

- 網域需要一個 KDS 根金鑰。
- 域中必須至少有一個 Windows 伺服器 2012（或 R2） DC。

1. 讓 Active Directory 域管理員使用`New-ADServiceAccount`Cmdlet 創建組託管服務帳戶，並確保`PrincipalsAllowedToRetrieveManagedPassword`包含所有服務結構叢集節點。 `AccountName`、`DnsHostName` 和 `ServicePrincipalName` 必須是唯一的。

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. 在每個 Service Fabric 叢集節點 (例如 `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`) 上，安裝並測試 gMSA。
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. 配置使用者主體，並將`RunAsPolicy`配置為引用[使用者](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas)。
    
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

以下文章將指導您完成以下步驟：

- [了解應用程式模型](service-fabric-application-model.md)
- [在服務資訊清單中指定資源](service-fabric-service-manifest-resources.md)
- [部署應用程式](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
