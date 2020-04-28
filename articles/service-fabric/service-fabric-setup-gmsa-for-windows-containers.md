---
title: 為 Azure Service Fabric 容器服務設定 gMSA
description: 立即瞭解如何為在 Azure Service Fabric 中執行的容器設定群組受管理的服務帳戶（gMSA）。
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75639202"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>為在 Service Fabric 上執行的 Windows 容器設定 gMSA

為了設定 gMSA (群組受控服務帳戶)，所有叢集節點上都會放置認證規格檔案 (`credspec`)。 您可以使用 VM 擴充功能將此檔案複製到所有節點上。  `credspec` 檔案必須包含 gMSA 帳戶資訊。 如需檔案的詳細`credspec`資訊，請參閱[建立認證規格](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)。認證規格和`Hostname`標記會在應用程式資訊清單中指定。 `Hostname` 標記必須符合用來執行容器的 gMSA 帳戶名稱。  `Hostname` 標記可讓容器使用 Kerberos 驗證向網域中的其他服務驗證其自身。  下列程式碼片段會顯示用來在應用程式資訊清單中指定 `Hostname` 和 `credspec` 的範例：

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
若要了解下一個步驟，請閱讀下列文章：

* [將 Windows 容器部署至 Windows Server 2016 上的 Service Fabric](service-fabric-get-started-containers.md)
* [將 Docker 容器部署至 Linux 上的 Service Fabric](service-fabric-get-started-containers-linux.md)
