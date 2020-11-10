---
title: Azure Hybrid Benefit 和 Linux Vm
description: 此 Azure Hybrid Benefit 可讓您在 Azure 上執行的 Linux 虛擬機器上節省成本。
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: feaa2471f2867257deb06ab32ed5fc0a26a0d37e
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443427"
---
# <a name="public-preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>公開預覽： Azure Hybrid Benefit –適用于 Linux 虛擬機器

## <a name="overview"></a>概觀

Azure Hybrid Benefit 可讓您使用自己既有的 Red Hat 或 SUSE software 訂用帳戶，更輕鬆地將您的內部部署 Red Hat Enterprise Linux (RHEL) 和 SUSE Linux Enterprise Server (的 SLES) 虛擬機器 (Vm) 至 Azure。 有了這項權益，您只需支付 VM 的基礎結構成本，因為您的 RHEL 或 SLES 訂用帳戶涵蓋了軟體費用。 此權益適用于所有 RHEL 和 SLES Marketplace 隨用隨付 (PAYG) 映射。

> [!IMPORTANT]
> Linux Vm 的 Azure Hybrid Benefit 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="benefit-description"></a>權益描述

透過 Azure Hybrid Benefit，您可以藉由將 Azure 上的現有 RHEL 和 SLES PAYG Vm 轉換成自備訂用帳戶 (BYOS) 帳單，更輕鬆地將內部部署 RHEL 和 SLES 伺服器遷移至 Azure。 一般而言，從 Azure 上的 PAYG 映射部署的 Vm 將會收取基礎結構費用和軟體費用。 使用 Azure Hybrid Benefit，PAYG Vm 可以轉換成 BYOS 計費模式，而不需重新部署，以避免任何停機風險。

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="在 Linux Vm 上 Azure Hybrid Benefit 成本視覺效果。":::

在 RHEL 或 SLES VM 上啟用權益時，您將不再需要支付 PAYG VM 上通常產生的額外軟體費用。 相反地，您的 VM 會開始發出 BYOS 費用，其中只包含計算硬體費用和無軟體費用。

如果您想要的話，也可以將已啟用權益的 VM 轉換回 PAYG 計費模型。

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Linux Vm 的 Azure Hybrid Benefit 資格範圍

Azure Hybrid Benefit 適用于所有 RHEL 和 SLES Marketplace PAYG 映射。 RHEL 或 SLES Marketplace BYOS 映射或自訂映射尚未提供此權益。

如果您已經使用 Linux Vm 的權益，保留實例、專用主機和 SQL 混合式權益不符合 Azure Hybrid Benefit 資格。

## <a name="how-to-get-started"></a>如何開始使用

Azure Hybrid Benefit 目前處於 Linux Vm 的預覽階段。 當您取得預覽版的存取權之後，您就可以使用 Azure CLI 來啟用權益。

### <a name="public-preview"></a>公開預覽

適用于 Linux) 的 Azure Hybrid Benefit (現已進入公開預覽階段。 您可以使用下列步驟來啟用 Red Hat 和 SUSE 分佈的權益。 

### <a name="red-hat-customers"></a>Red Hat 客戶

1.    向[Red Hat Cloud Access 方案](https://aka.ms/rhel-cloud-access)註冊
1.    啟用您的 Azure 訂用帳戶 () 進行雲端存取，並啟用包含您想要使用權益之 Vm 的訂用帳戶
1.    透過 Azure CLI 將權益套用至現有的 Vm
1.    使用個別的更新來源來註冊您的 Vm 以接收權益


### <a name="suse-customers"></a>SUSE 客戶

1.    向 SUSE Public Cloud 方案註冊
1.    透過 Azure CLI 將權益套用至現有的 Vm
1.    使用個別的更新來源來註冊您的 Vm 以接收權益


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>在 Azure CLI 中啟用和停用權益

您可以使用 ' az vm update ' 命令來更新現有的 Vm。 針對 RHEL Vm，請以 "RHEL_BYOS" 的--授權類型參數執行命令。 針對 SLES Vm，請使用 "SLES_BYOS" 的--授權類型參數執行命令。

#### <a name="cli-example-to-enable-the-benefit"></a>啟用權益的 CLI 範例：
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>停用權益的 CLI 範例：
若要停用此權益，請使用「無」授權類型值
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>在大量 Vm 上啟用權益的 CLI 範例
若要在大量的 Vm 上啟用權益，您可以 `--ids` 在 Azure CLI 中使用參數。

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

下列範例顯示兩個取得資源識別碼清單的方法：一個在資源群組層級，一個在訂用帳戶層級。
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>檢查 VM 的 AHB 狀態
您可以透過兩種方式來查看 VM 的 AHB 狀態：使用 Azure CLI，或使用 Azure Instance Metadata Service (Azure IMDS) 。


### <a name="azure-cli"></a>Azure CLI

`az vm get-instance-view`此命令可用於此用途。 在回應中尋找 licenseType 欄位。 如果 licenseType 欄位存在且值為「RHEL_BYOS」或「SLES_BYOS」，則您的 VM 已啟用權益。

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務

從 VM 本身中，您可以查詢 IMDS 證明中繼資料來判斷 VM 的 licenseType。 LicenseType 值 ' RHEL_BYOS ' 或 ' SLES_BYOS ' 會指出您的 VM 已啟用權益。 [在此](./instance-metadata-service.md#attested-data)深入瞭解證明中繼資料

## <a name="compliance"></a>法規遵循

### <a name="red-hat"></a>Red Hat

若要使用適用于 RHEL Vm 的 Azure Hybrid Benefit，您必須先向 Red Hat Cloud Access 方案註冊。 您可以透過這裡的 Red Hat Cloud Access 網站來完成此作業。 當您在 VM 上啟用權益之後，您必須使用您自己的更新來源（具有 Red Hat 訂用帳戶管理員或 Red Hat 附屬物）註冊 VM。 註冊更新將可確保您保持在支援的狀態。

### <a name="suse"></a>SUSE

若要將 Azure Hybrid Benefit 用於 SLES Vm，您必須先向 SUSE Public Cloud 方案註冊。 若要深入瞭解此程式，請參閱。 購買 SUSE 訂用帳戶之後，您必須使用 SUSE 客戶中心、訂用帳戶管理工具伺服器或 SUSE Manager，在您自己的更新來源使用這些訂用帳戶註冊您的 Vm。

## <a name="frequently-asked-questions"></a>常見問題集
*問：我是否可以使用「RHEL_BYOS」授權類型搭配 SLES 映射，反之亦然？*

答：不可以。 如果嘗試輸入不正確符合您 VM 上執行之發行版本的授權類型，將不會更新任何帳單中繼資料。 不過，如果您不小心輸入了錯誤的授權類型，則再次將 VM 更新為正確的授權類型仍會啟用權益。

*問：我已向 Red Hat Cloud Access 註冊，但仍無法在 RHEL Vm 上啟用權益。我該怎麼辦？*

答：您的 Red Hat Cloud Access 訂用帳戶註冊可能需要一些時間才能從 Red Hat 傳播至 Azure。 如果您在一個工作天之後仍看到錯誤，請洽詢 Microsoft 支援服務。

## <a name="common-issues"></a>常見問題
本節包含可能遇到的常見問題清單，以及緩和措施的步驟。

| 錯誤 | 降低 |
| ----- | ---------- |
| 「無法完成動作，因為我們的記錄顯示您尚未成功啟用 Azure 訂用帳戶上的 Red Hat Cloud 存取 ...」。 | 若要使用 RHEL Vm 的優點，您必須先向 Red Hat Cloud Access 註冊您的 Azure 訂用帳戶 () 。 請流覽此連結，以深入瞭解如何註冊適用于 Red Hat Cloud Access 的 Azure 訂用帳戶

## <a name="next-steps"></a>後續步驟
* 瞭解如何建立和更新 Vm，並 (RHEL_BYOS、SLES_BYOS) Azure Hybrid Benefit[在這裡](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)使用 Azure CLI 來新增授權類型。
