---
title: 虛擬機器上的 Azure 機密運算解決方案
description: 了解虛擬機器上的 Azure 機密運算解決方案。
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: f9b73e0919d660947edd0417f7379b3f6e6140c0
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245847"
---
# <a name="solutions-on-azure-virtual-machines"></a>Azure 虛擬機器上的解決方案

此文章涵蓋部署 Azure 機密運算虛擬機器 (VM) 的相關資訊，這類虛擬機器會執行 [Intel Software Guard Extensions](https://software.intel.com/sgx) \(Intel SGX\) 所支援的 Intel 處理器。 

## <a name="azure-confidential-computing-vm-sizes"></a>Azure 機密計算 VM 大小

Azure 機密運算虛擬機器的設計目的是，在雲端中處理資料與程式碼時，保護其機密性和完整性 

[DCsv2 系列](../virtual-machines/dcv2-series.md)的 VM 是最新且最近使用的機密運算大小系列。 相較於我們 DC 系列的 VM，這些 VM 支援較大範圍的部署功能、具有 2 倍的記憶體保護區頁面快取 (EPC)，以及較大的大小選擇。 [DC 系列](../virtual-machines/sizes-previous-gen.md#preview-dc-series)的 VM 目前處於預覽狀態，即將淘汰且不會隨附於公開推出版本中。

藉由遵循[快速入門教學課程](quick-create-marketplace.md)，透過 Microsoft 商業市場，開始部署 DCsv2 系列的 VM。

### <a name="current-available-sizes-and-regions"></a>目前可用的大小和區域

若要取得可用區域和可用性區域中所有正式發行的機密運算 VM 大小清單，請在 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) 中執行下列命令：

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

如需上述大小的更詳細資訊，請執行下列命令：

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>專用主機需求
在 DCSv2 系列的 VM 系列中部署 **Standard_DC8_v2** 虛擬機器大小將會佔用完整的主機，且不會與其他租使用者或訂用帳戶共用。 此 VM SKU 系列會提供您所需的隔離，以符合通常藉由擁有專用主機服務的合規性和安全性法規需求。 當您選擇 **Standard_DC8_v2** SKU 時，實體主機伺服器會將所有可用的硬體資源（包括僅限 EPC 記憶體）配置給您的虛擬機器。 請注意，這項功能是由基礎結構設計所存在，而且會支援 **Standard_DC8_v2** 的所有功能。 此部署與其他 Azure VM 系列所提供的 [Azure 專用主機](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts) 服務不同。


## <a name="deployment-considerations"></a>部署考量因素

遵循快速入門教學課程，以便在 10 分鐘內部署 DCsv2 系列的虛擬機器。 

- **Azure 訂用帳戶**：若要部署機密運算 VM 執行個體，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，將不會有適當數量的 Azure 計算核心配額。

- **定價和區域可用性**：在[虛擬機器定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)上，尋找 DCsv2 系列 VM 的定價。 如需了解 Azure 區域中的可用性，請查看 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 。


- **核心配額** – 您可能需要從預設值增加 Azure 訂用帳戶的核心配額。 您的訂用帳戶可能也會限制您可以在特定 VM 大小系列 (包括 DCsv2 系列) 中部署的核心數目。 若要要求增加配額，可免費[開啟線上客戶支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)。 請注意，預設限制可能會視您的訂用帳戶類別而有所不同。

  > [!NOTE]
  > 如果您有大規模的容量需求，請連絡 Azure 支援。 Azure 配額為信用額度，而不是容量保證。 無論您的配額有多少，您只需針對您使用的核心付費。
  
- **調整大小**：由於其特殊硬體，您只能在相同大小系列內調整機密運算執行個體的大小。 例如，您只能將 DCsv2 系列 VM 的大小，從某一個 DCsv2 系列大小調整為另一個大小。 不支援從非機密運算大小調整為機密運算大小。  

- **映像**：若要在機密運算執行個體上提供 Intel Software Guard Extensions (Intel SGX) 支援，所有部署都必須在第 2 代映像上執行。 Azure 機密運算支援在 Ubuntu 18.04 Gen 2、Ubuntu 16.04 Gen 2、Windows Server 2019 gen2 和 Windows Server 2016 Gen 2 上執行的工作負載。 若要深入了解支援和不支援的案例，請參閱 [Azure 上第 2 代 VM 的支援](../virtual-machines/linux/generation-2.md)。 

- **儲存體**：Azure 機密運算虛擬機器資料磁碟和我們的暫時性 OS 磁碟都位於 NVMe 磁碟上。 執行個體僅支援進階 SSD 和標準 SSD 磁碟，而不支援 Ultra SSD 或標準 HDD。 虛擬機器大小 **DC8_v2** 不支援進階儲存體。 

- **磁碟加密**：機密運算執行個體目前不支援磁碟加密。 

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和災害復原考量

在 Azure 中使用虛擬機器時，您必須負責實作高可用性和災害復原解決方案，以避免任何停機。 

Azure 機密運算目前不支援透過可用性區域進行區域備援。 若要取得適用於機密運算的最高可用性與備援，請使用[可用性設定組](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。 由於硬體限制，機密運算執行個體的可用性設定組最多只能有 10 個更新網域。 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>使用 Azure Resource Manager (ARM) 範本進行部署

Azure Resource Manager 是 Azure 的部署和管理服務。 其提供管理層，可讓您建立、更新和刪除您 Azure 訂用帳戶中的資源。 您可以使用存取控制、鎖定和標記等管理功能，在部署後保護及組織您的資源。

若要深入瞭解 ARM 範本，請參閱 [範本部署總覽](../azure-resource-manager/templates/overview.md)。

若要在 ARM 範本中部署 DCsv2 系列 VM，您將使用 [虛擬機器資源](../virtual-machines/windows/template-description.md)。 請務必為 **vmSize** 和您的 **imageReference** 指定正確屬性。

### <a name="vm-size"></a>VM 大小

在虛擬機器資源的 ARM 範本中指定下列其中一種大小。 這個字串會以 **vmSize** 形式放在 **properties** 中。

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS 映射

在 **properties** 底下，您也必須參考 **storageProfile** 底下的映像。 針對您的 **imageReference**，「僅使用一個」下列映像。

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>後續步驟 

在此文章中，您已了解建立機密運算虛擬機器時所需的資格和設定。 您現在可以前往 Microsoft Azure Marketplace，以部署 DCsv2 系列的 VM。

> [!div class="nextstepaction"]
> [在 Azure Marketplace 中部署 DCsv2 系列的虛擬機器](quick-create-marketplace.md)
