---
title: Azure 映射產生器服務網路功能選項
description: 瞭解部署 Azure VM Image Builder 服務時的網路功能選項
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 531c423e42338b72b41c54466d5bfe8a89cd3c45
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969632"
---
# <a name="azure-image-builder-service-networking-options"></a>Azure 映射產生器服務網路功能選項

您必須選擇部署 Azure 映射產生器，但不含現有的 VNET。

## <a name="deploy-without-specifying-an-existing-vnet"></a>部署而不指定現有的 VNET

如果您未指定現有的 VNET，Azure 映射建立器會在預備資源群組中建立 VNET 和子網。 公用 IP 資源會與網路安全性群組搭配使用，以限制對 Azure 映射產生器服務的輸入流量。 公用 IP 可在映射組建期間協助 Azure 映射產生器命令的通道。 組建完成後，就會刪除 VM、公用 IP、磁片和 VNET。 若要使用此選項，請勿指定任何 VNET 屬性。

## <a name="deploy-using-an-existing-vnet"></a>使用現有的 VNET 部署

如果您指定 VNET 和子網，Azure 映射產生器會將組建 VM 部署至您選擇的 VNET。 您可以存取可在 VNET 上存取的資源。 您也可以建立未連線至任何其他 VNET 的孤立 VNET。 如果您指定 VNET，Azure 映射產生器不會使用公用 IP 位址。 從 Azure 映射產生器服務到組建 VM 的通訊會使用 Azure Private Link 技術。

如需詳細資訊，請參閱下列其中一個範例：

* [針對允許存取現有 Azure VNET 的 Windows Vm 使用 Azure 映射產生器](../windows/image-builder-vnet.md)
* [使用適用于 Linux Vm 的 Azure 映射產生器，以允許存取現有的 Azure VNET](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>什麼是 Azure Private Link？

Azure Private Link 提供從虛擬網路到 Azure 平臺即服務 (PaaS) 、客戶擁有或 Microsoft 合作夥伴服務的私人連線。 它可簡化網路架構，並藉由消除公用網際網路的資料暴露，來保護 Azure 中端點之間的連線。 如需詳細資訊，請參閱 [Private Link 檔](../../private-link/index.yml)。

### <a name="required-permissions-for-an-existing-vnet"></a>現有 VNET 的必要許可權

Azure 映射產生器需要特定的許可權，才能使用現有的 VNET。 如需詳細資訊，請參閱 [使用 Azure CLI 設定 Azure 映射產生器服務許可權](image-builder-permissions-cli.md) 或 [使用 PowerShell 設定 azure 映射產生器服務許可權](image-builder-permissions-powershell.md)。

### <a name="what-is-deployed-during-an-image-build"></a>映射組建期間會部署什麼？

使用現有的 VNET 表示 Azure 映射產生器會 (proxy VM) 部署額外的 VM，以及連線到) 的 Azure Load Balancer (ALB Private Link。 來自 AIB 服務的流量會跨該 ALB 的私用連結。 ALB 會使用適用于 Linux OS 的埠60001或適用于 Windows 作業系統的60000，與 proxy VM 通訊。 Proxy 會使用適用于 Linux OS 的埠22或適用于 Windows OS 的5986，將命令轉送至組建 VM。

> [!NOTE]
> VNET 必須與 Azure 映射產生器服務區域位於相同的區域。
> 

### <a name="why-deploy-a-proxy-vm"></a>為何要部署 Proxy VM？

如果沒有公用 IP 的 VM 位於內部 Load Balancer，則無法存取網際網路。 用於 VNET 的 Azure Load Balancer 是內部的。 Proxy VM 允許在組建期間對組建 VM 進行網際網路存取。 相關聯的網路安全性群組可以用來限制組建 VM 的存取。

除了組建 VM 之外，已部署的 proxy VM 大小是標準 A1_v2。 Proxy VM 可用來在 Azure 映射產生器服務和組建 VM 之間傳送命令。 Proxy VM 屬性無法變更，包括大小或 OS。 您無法變更 Windows 和 Linux 映射組建的 proxy VM 屬性。

### <a name="image-template-parameters-to-support-vnet"></a>支援 VNET 的影像範本參數
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| 設定 | 描述 |
|---------|---------|
| NAME |  (預先存在之虛擬網路的選擇性) 名稱。 |
| subnetName | 指定之虛擬網路內的子網名稱。 只有在指定 *name* 時，才必須指定。 |
| resourceGroupName | 包含指定虛擬網路的資源組名。 只有在指定 *name* 時，才必須指定。 |

Private Link 服務需要指定的 VNET 和子網中的 IP。 Azure 目前不支援這些 Ip 上的網路原則。 因此，子網必須停用網路原則。 如需詳細資訊，請參閱 [Private Link 檔](../../private-link/index.yml)。

### <a name="checklist-for-using-your-vnet"></a>使用 VNET 的檢查清單

1. 允許 Azure Load Balancer (ALB) 與 NSG 中的 proxy VM 進行通訊
    * [AZ CLI 範例](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell 範例](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. 在子網上停用私人服務原則
    * [AZ CLI 範例](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell 範例](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. 允許 Azure Image Builder 建立 ALB 並將 Vm 新增至 VNET
    * [AZ CLI 範例](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell 範例](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. 允許 Azure 映射產生器讀取/寫入來源映射並建立映射
    * [AZ CLI 範例](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell 範例](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. 確定您在與 Azure 映射產生器服務區域相同的區域中使用 VNET。


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure 映射](image-builder-overview.md)建立器總覽。