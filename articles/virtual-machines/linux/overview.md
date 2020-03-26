---
title: Azure 中 Linux VM 的概觀
description: Azure 中的 Linux 虛擬機器概觀。
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 69a9722436aea1cf794e6e3f3ce02ec79180cff3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80159467"
---
# <a name="linux-virtual-machines-in-azure"></a>Azure 中的 Linux 虛擬機器

Azure 虛擬機器 (VM) 是由 Azure 所提供的[隨選且可調整的數種運算資源](/azure/architecture/guide/technology-choices/compute-decision-tree)類型之一。 一般而言，當您對於運算環境所需的控制權比其他選擇可提供的還要多時，則您會選擇 VM。 本文提供您在建立 VM 之前應該的事項、建立方式及管理方式的相關資訊。

Azure VM 讓您能夠有彈性地進行虛擬化，而不需購買並維護執行它的實體硬體。 不過，您仍然需要執行工作來維護 VM，例如設定、修補和安裝在 VM 上執行的軟體。

Azure 虛擬機器可用於許多用途。 部份範例如下：

* **開發和測試** – Azure VM 提供了一個快速且簡單的方法，用以建立為應用程式撰寫程式碼並進行測試時所需之特定組態的電腦。
* **雲端中的應用程式** – 因為您應用程式的需求可能會變動，在 Azure VM 上執行它在經濟上是合理的。 當您需要 VM 時便支付額外的 VM，而當您不需要時便關閉這些 VM。
* **擴充的資料中心** – 可輕鬆將 Azure 虛擬網路中的虛擬機器連線到您組織的網路。

您的應用程式所使用的 VM 數目可以擴大及擴增為符合您需求的任何內容。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>我在建立 VM 之前需要先考慮什麼？
當您在 Azure 中建置應用程式基礎結構時，總是會有許多[設計考量](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm)。 在您開始之前，仔細考量 VM 的這些層面很重要︰

* 應用程式資源的名稱
* 將儲存資源的位置
* VM 的大小
* 可建立的 VM 數目上限
* VM 上執行的作業系統
* VM 啟動後的設定
* VM 需要的相關資源

### <a name="locations"></a>位置
Azure 中所建立的所有資源分散在世界各地的多個[地理區域](https://azure.microsoft.com/regions/)。 通常，當您建立 VM 時，區域稱為**位置**。 針對 VM，位置會指定虛擬硬碟所儲存的位置。

下表顯示一些您可以取得可用位置清單的方式。

| 方法 | 描述 |
| --- | --- |
| Azure 入口網站 |當您建立 VM 時，請從清單中選取位置。 |
| Azure PowerShell |使用 [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) 命令。 |
| REST API |使用[列出位置](https://docs.microsoft.com/rest/api/resources/subscriptions)作業。 |
| Azure CLI |使用 [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest) 作業。 |

## <a name="availability"></a>可用性
Azure 已宣布推出業界領先的單一執行個體虛擬機器 99.9% 服務等級協定 (前提是您部署的所有磁碟都是進階儲存體的 VM)。  為了讓您的部署符合標準的 99.95% VM 服務等級協定資格，您還必須在可用性設定組內部署兩個或更多執行工作負載的 VM。 可用性設定組可確保您的 VM 會分散在 Azure 資料中心內的多個容錯網域，以及部署至具有不同維護期間的主機。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 說明保證的 Azure 整體可用性。

## <a name="vm-size"></a>VM 大小
您使用的 VM [大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)是由所您要執行的工作負載所決定。 您所選的大小會決定例如處理電源、記憶體和儲存體容量等因素。 Azure 提供了各種不同的大小，以支援許多類型的用法。

Azure 可依據 VM 的大小和作業系統，以[每小時](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)價格方式收費。 針對不足一小時的部分，Azure 只會收取已使用分鐘數的費用。 儲存體是個別定價與收費。

## <a name="vm-limits"></a>VM 限制
您的訂用帳戶都有預設[配額限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)，而此限制會在您要部署多個 VM 以供專案使用時造成影響。 每一訂用帳戶目前的限制是每一區域 20 個 VM。 只要[提出支援票證來要求增加](../../azure-portal/supportability/resource-manager-core-quotas-request.md)，即可提高配額限制

## <a name="managed-disks"></a>受控磁碟

受控磁碟可在背景中為您處理 Azure 儲存體帳戶的建立和管理作業，確保您不需要擔心儲存體帳戶的延展性限制。 您可指定磁碟大小和效能層級 (標準或進階)，而 Azure 會建立和管理磁碟。 當您新增磁碟或相應增加和減少 VM 時，都不必擔心所使用的儲存體。 如果您要建立新的 VM，請[使用 Azure CLI](quick-create-cli.md) 或 Azure 入口網站來建立具有受控 OS 和資料磁碟的 VM。 如果您有具備非受控磁碟的 VM，您可以[將 VM 轉換成由受控磁碟提供支援](convert-unmanaged-to-managed-disks.md)。

您也可以在每個 Azure 區域中使用單一儲存體帳戶管理自訂映像，並使用映像在相同訂用帳戶中建立數百個 VM。 如需受控磁碟的詳細資訊，請參閱 [Azure 受控磁碟概觀](../linux/managed-disks-overview.md)。

## <a name="distributions"></a>散發 
Microsoft Azure 支援執行由多家合作夥伴提供和維護的眾多熱門 Linux 散發套件。  您可以在 Azure Marketplace 中找到 Red Hat Enterprise、CentOS、SUSE Linux Enterprise、Debian、Ubuntu、CoreOS、RancherOS 及 FreeBSD 等散發套件。 Microsoft 與各個 Linux 社群積極合作，以便為[經 Azure 背書的 Linux 散發套件](endorsed-distros.md)清單新增更多版本選項。

如果您慣用的 Linux 散發套件選項目前未出現在映像庫中，您可以[建立 Linux VHD 並上傳到 Azure](create-upload-generic.md)來「自備 Linux」VM。

Microsoft 與合作夥伴密切合作，以確保更新可用的映像並針對 Azure 執行階段進行最佳化。  如需 Azure 合作夥伴的詳細資訊，請參閱下列連結：

* [Azure 背書散發套件上的 Linux](endorsed-distros.md)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat - [Azure Marketplace - Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [適用於 Azure 的 Bitnami 程式庫](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Azure 上的 Mesosphere DC/OS](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace - 與 Docker Swarm 搭配使用的 Azure Container Service](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

為了實現適當的 DevOps 文化特性，所有基礎結構都必須是程式碼。  當所有基礎結構都留存在程式碼中時，便可以輕鬆地重新建立基礎結構。  Azure 可以與所有主要的自動化工具 (例如 Ansible、Chef、SaltStack 及 Puppet) 搭配運作。  Azure 也有自己的自動化工具：

* [Azure 範本](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

在支援 [cloud-init](https://cloud-init.io/) 的多數 Linux 散發版本上，Azure 均支援 cloud-init。  我們一直積極地與背書的 Linux 發行版本合作夥伴合作，以便在 Azure Marketplace 中提供支援 Cloud-init 的映像。 這些映像會讓您的 Cloud-init 部署和設定順暢地與 VM 和虛擬機器擴展集運作。

* [在 Azure Linux VM 上使用 cloud-init](using-cloud-init.md)

## <a name="storage"></a>儲存體
* [Microsoft Azure 儲存體簡介](../../storage/common/storage-introduction.md)
* [使用 azure-cli 將磁碟新增到 Linux VM](add-disk.md)
* [如何在 Azure 入口網站中將資料磁碟連結到 Linux VM](attach-disk-portal.md)

## <a name="networking"></a>網路功能
* [虛擬網路概觀](../../virtual-network/virtual-networks-overview.md)
* [Azure 中的 IP 位址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [對 Azure 中的 Linux VM 開啟連接埠](nsg-quickstart.md)
* [在 Azure 入口網站中建立完整格式的網域名稱](portal-create-fqdn.md)


## <a name="next-steps"></a>後續步驟

建立您的第一個 VM！

- [入口網站](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)

