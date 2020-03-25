---
title: Azure PowerShell 範例 - Service Fabric
description: 了解如何使用 Powershell 建立和管理 Azure Service Fabric 叢集、應用程式和服務。
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: f9ba1b4833b3aff13284a68c23f398ea47ef2ae2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75645645"
---
# <a name="azure-service-fabric-powershell-samples"></a>Azure Service Fabric PowerShell 範例

下表包含 PowerShell 指令碼範例的連結，這個範例建立和管理 Service Fabric 叢集、應用程式和服務。

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **建立叢集** ||
| [建立叢集 (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| 建立 Azure Service Fabric 叢集。 |
| **管理叢集、節點和基礎結構** ||
| [新增應用程式憑證](./scripts/service-fabric-powershell-add-application-certificate.md)| 建立 Key Vault 的 X509 憑證，並將其部署至叢集中的虛擬機器擴展集。 |
| [更新叢集 VM 上的 RDP 連接埠範圍](./scripts/service-fabric-powershell-change-rdp-port-range.md)|對已部署之叢集中的叢集節點 VM 變更 RDP 連接埠範圍。|
| [更新叢集節點 VM 的管理員使用者和密碼](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | 更新叢集節點 VM 的管理員使用者名稱和密碼。 |
| [在負載平衡器中開啟連接埠](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | 在 Azure 負載平衡器中開啟應用程式連接埠，以在特定連接埠上允許輸入流量。 |
| [建立輸入網路安全性群組規則](./scripts/service-fabric-powershell-add-nsg-rule.md) | 建立輸入網路安全性群組規則，以在特定連接埠上允許叢集的輸入流量。 |
| **管理應用程式** ||
| [部署應用程式](./scripts/service-fabric-powershell-deploy-application.md)| 將應用程式部署到叢集。|
| [升級應用程式](./scripts/service-fabric-powershell-upgrade-application.md)| 升級應用程式。|
| [移除應用程式](./scripts/service-fabric-powershell-remove-application.md)| 從叢集移除應用程式。|
