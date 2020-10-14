---
title: Azure 上的 Red Hat 工作負載概觀 | Microsoft Docs
description: 了解 Azure 上可用的 Red Hat 產品供應項目。
author: asinn826
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 61f7461633aba18a21fcc18709055dc790ffee7b
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993231"
---
# <a name="red-hat-workloads-on-azure"></a>Azure 上的 Red Hat 工作負載

Red Hat 工作負載可透過 Azure 上的各種供應項目來獲得支援。 和 Red Hat 更新基礎結構 (RHUI) 一樣，Red Hat Enterprise Linux (RHEL) 映像位於 RHEL 工作負載的核心。

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 映像

Azure 上提供了各式各樣的 RHEL 映像。 這些映像可透過兩種不同的授權模型來取得：隨用隨付和自備訂用帳戶 (BYOS)。 Azure 上的新 RHEL 映像會在有新發行的 RHEL 版本時發佈，並視需要在其生命週期內進行更新。

### <a name="pay-as-you-go-images"></a>隨用隨付映像

Azure 會提供各種不同的 RHEL 隨用隨付映像。 這些映像在推出時便已獲得正確的 RHEL 權限，並且會連結至更新的來源 (Red Hat 更新基礎結構)。 這些映像會針對 RHEL 權利和更新收取進階費用。 RHEL 隨用隨付映像變體包含：

* 標準 RHEL。
* RHEL for SAP。
* RHEL for SAP (含高可用性和更新服務)。

如果您不想煩惱是否要另外付費來取得適當數量的訂用帳戶，則可以使用隨用隨付映像。

### <a name="red-hat-gold-images"></a>Red Hat Gold 映像

Azure 也提供 Red Hat Gold 映像 (`rhel-byos`)。 對於已有 Red Hat 訂用帳戶且想要在 Azure 中使用這些訂用帳戶的客戶而言，這些映像可能會很有用。 您必須先為 Red Hat 雲端存取啟用現有的 Red Hat 訂用帳戶，才能在 Azure 中使用這些訂用帳戶。 當您為雲端存取啟用 Red Hat 訂用帳戶，並且符合資格需求時，系統就會自動授與這些映像的存取權。 使用這些映像可讓客戶避免因使用隨用隨付映像而可能產生的重複計費情形。
* 了解如何[透過 Azure 為雲端存取啟用您的 Red Hat 訂用帳戶](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)。
* 了解如何[在 Azure 入口網站、Azure CLI 或 PowerShell Cmdlet 中尋找 Red Hat Gold 映像](./byos.md)。

> [!NOTE]
> 當使用者針對 RHEL 訂用帳戶支付兩次費用時，就會產生重複計費。 這種情況通常發生在客戶使用訂用帳戶管理員來連結 RHEL 隨用隨付 VM 上的權利時。 例如，使用訂用帳戶管理員在 RHEL 隨用隨付映像上連結 SAP 套件權利的客戶，系統會間接地加倍計費，因為客戶針對 RHEL 支付了二次費用。 他們會透過隨用隨付進階費用支付一次，再透過其 SAP 訂用帳戶支付一次。 BYOS 映像使用者並不會發生這種情況。

### <a name="generation-2-images"></a>第 2 代映像

第 2 代虛擬機器 (VM) 會提供一些比第 1 代 VM 還要新的功能。 如需詳細資訊，請參閱[第 2 代 文件](../../generation-2.md)。 與 RHEL 映像的觀點來看，主要差異在於第 2 代 VM 會使用 UEFI 而非 BIOS 韌體介面； 同時也會在開機時使用 GUID 磁碟分割表格 (GPT)，而不是主開機記錄 (MBR)。 不只如此，使用 GPT 還可讓您使用大於 2 TB 的 OS 磁碟大小。 此外，[Mv2 系列 VM](../../mv2-series.md) 只會在第 2 代映像上執行。

RHEL 第 2 代映像可在 Azure Marketplace 中取得。 使用 Azure CLI 時，在顯示的所有映像清單中，尋找映像 SKU 中的 "gen2"。 移至 VM 部署流程中的 [進階] 索引標籤，以部署第 2 代 VM。

## <a name="red-hat-update-infrastructure"></a>Red Hat 更新基礎結構

Azure 僅針對隨用隨付 RHEL VM 提供 Red Hat 更新基礎結構。 RHUI 實際上是 Red Hat CDN 的鏡像，但只能供 Azure 隨用隨付 RHEL VM 存取。 視已部署的 RHEL 映像而定，您可以存取適當的套件。 例如，RHEL for SAP 映像除了可存取基底 RHEL 套件外，也可以存取 SAP 套件。

### <a name="rhui-update-behavior"></a>RHUI 更新行為

根據預設，連線至 RHUI 的 RHEL 映像將會在執行 `yum update` 時，更新至最新的 RHEL 次要版本。 此行為表示，如果 RHEL 7.4 VM 上執行了 `yum update` 作業，便可能會升級至 RHEL 7.7。 這是針對 RHUI 刻意設計的行為。 您可以從一般 RHEL 存放庫切換到[擴充更新支援存放庫](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)來減輕此升級行為可能帶來的影響。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure 上的 RHEL 映像](./redhat-images.md)。
* 深入了解 [Red Hat 更新基礎結構](./redhat-rhui.md)。
* 深入了解 [Red Hat Gold 映像 (`rhel-byos`) 供應項目](./byos.md)。