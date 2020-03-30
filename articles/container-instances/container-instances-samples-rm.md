---
title: Azure Resource Manager 範本範例
description: 查找 Azure 資源管理器範本示例，以部署不同配置中的 Azure 容器實例
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981656"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure 容器執行個體的 Azure Resource Manager 範本

下列範例範本會在各種組態中部署容器執行個體。

如需部署選項，請參閱[部署](#deployment)一節。 如果您想要建立您自己的範本，Azure 容器執行個體 [Resource Manager 範本參考][ref]會詳細說明範本格式和可用的屬性。

## <a name="sample-templates"></a>範例範本

| | |
|-|-|
| **應用程式** ||
| [Wordpress][app-wp] | 在容器組中創建 WordPress 網站及其 MySQL 資料庫。 WordPress 網站內容和 MySQL 資料庫會保存到 Azure 檔案服務共用中。 還創建一個應用程式閘道來公開對 WordPress 的公共網路訪問。 |
| [MS NAV 與 SQL Server 和 IIS][app-nav] | 以具有完整功能的獨立式 Dynamics NAV / Dynamics 365 Business Central 環境部署單一 Windows 容器。 |
| **卷** ||
| [emptyDir][vol-emptydir] | 部署共用 emptyDir 磁碟區的兩個 Linux 容器。 |
| [gitRepo][vol-gitrepo] | 部署會複製 GitHub 存放庫，並將其掛接為磁碟區的 Linux 容器。 |
| [秘密][vol-secret] | 使用掛接為祕密磁碟區的 PFX 憑證來部署 Linux 容器。 |
| **網路功能** ||
| [UDP 公開容器][net-udp] | 部署會公開 UDP 連接埠的 Windows 或 Linux 容器。 |
| [具有公用 IP 的 Linux 容器][net-publicip] | 部署可透過公用 IP 存取的單一 Linux 容器。 |
| [使用虛擬網路部署容器組（預覽）][net-vnet] | 部署新的虛擬網路、子網、網路設定檔和容器組。 |
| **Azure 資源** ||
| [建立 Azure 儲存體帳戶和檔案共用][az-files] | 在容器執行個體中使用 Azure CLI 以建立儲存體帳戶和 Azure 檔案服務共用。

## <a name="deployment"></a>部署

您有數個選項可以使用 Resource Manager 範本來部署資源：

[Azure CLI][deploy-cli]

[Azure 電源外殼][deploy-powershell]

[Azure 門戶][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
