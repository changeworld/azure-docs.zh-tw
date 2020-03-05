---
title: Azure 上的 Red Hat 工作負載概觀 | Microsoft Docs
description: 了解 Azure 上可用的 Red Hat 產品供應項目
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 424ef37885d685829a11d1864a72b043a562231c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920547"
---
# <a name="red-hat-workloads-on-azure"></a>Azure 上的 Red Hat 工作負載
Red Hat 工作負載可透過 Azure 上的各種供應項目來獲得支援。 和 Red Hat 更新基礎結構 (RHUI) 一樣，Red Hat Enterprise Linux (RHEL) 映像位於 RHEL 工作負載的核心。

## <a name="red-hat-enterprise-linux-rhel-images"></a>Red Hat Enterprise Linux (RHEL) 映像
Azure 上提供了各式各樣的 RHEL 映像。 這些映像可透過兩種不同的授權模型來取得：隨用隨付 (PAYG) 和自備訂用帳戶 (BYOS)。 Azure 上的新 RHEL 映像會在有新發行的 RHEL 版本時發佈，並視需要在其生命週期內進行更新。

### <a name="pay-as-you-go-payg-images"></a>隨用隨付 (PAYG) 映像
Azure 會提供各種不同的 RHEL PAYG 映像。 這些映像在推出時便已獲得正確的 RHEL 權限，並且會連結至更新的來源 (Red Hat 更新基礎結構)。 這些映像會針對 RHEL 權利和更新收取進階費用。 RHEL PAYG 映像變體包含：
* 標準 RHEL
* RHEL for SAP
* RHEL for SAP (含高可用性和更新服務)。

如果您不想要煩惱是否要另外付費來取得適當數量的訂用帳戶，則可以使用 PAYG 映像。

### <a name="red-hat-gold-images-rhel-byos"></a>Red Hat Gold 映像 (`rhel-byos`)
Azure 也提供 Red Hat Gold 映像。 對於已有 Red Hat 訂用帳戶且想要在 Azure 中使用這些訂用帳戶的客戶而言，這些映像可能會很有用。 您必須先為 Red Hat 雲端存取啟用現有的 Red Hat 訂用帳戶，才能在 Azure 中使用這些訂用帳戶。 當您為雲端存取啟用 Red Hat 訂用帳戶，並且符合資格需求時，系統就會自動授與這些映像的存取權。 使用這些映像可讓客戶避免因使用 PAYG 映像而可能產生的重複計費情形。
* [了解如何透過 Azure 為雲端存取啟用您的 Red Hat 訂用帳戶](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [了解如何在 Azure 入口網站、CLI 或 PowerShell Cmdlet 中尋找 Red Hat Gold 映像](./byos.md)

> [!NOTE]
> 關於重複計費的注意事項：當使用者針對 RHEL 訂用帳戶支付兩次費用時，就會產生重複計費。 這種情況通常發生在客戶使用 subscription-manager 來連結 RHEL PAYG VM 上的權利時。 例如，若客戶使用 subscription-manager 來連結 RHEL PAYG 映像上 SAP 套件的權利，便會間接發生重複計費的情形，原因是其會針對 RHEL 支付兩次費用，一次是透過 PAYG 進階費用，一次則是透過其 SAP 訂用帳戶。 BYOS 映像的使用者則不會發生這種情況。

### <a name="generation-2-images"></a>第 2 代映像
第 2 代 VM 會提供一些比第 1 代 VM 還要新的功能。 [第 2 代文件](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)中會有詳細資料。 與 RHEL 映像觀點的主要差異在於，第 2 代 VM 會使用 UEFI 而不是 BIOS 韌體介面，並且會在開機時使用 GUID 磁碟分割表格 (GPT) 而不是主開機記錄 (MBR)。 不只如此，這還可讓您使用大於 2 TB 的 OS 磁碟大小。 此外，[Mv2 系列 VM](../../mv2-series.md) 只會在第 2 代映像上執行。

RHEL 第 2 代映像可在市集中取得。 當您使用 Azure CLI 列出所有映像時，請在映像 SKU 中尋找 "gen2"，並在 VM 部署程序中移至 [進階] 索引標籤以部署第 2 代 VM。

## <a name="red-hat-update-infrastructure-rhui"></a>Red Hat 更新基礎結構 (RHUI)
Azure 僅針對 PAYG RHEL 虛擬機器 (VM) 提供 Red Hat 更新基礎結構。 RHUI 實際上是 Red Hat CDN 的鏡像，但只能供 Azure PAYG RHEL VM 存取。 視已部署的 RHEL 映像而定，您將可以存取適當的套件。 例如，RHEL for SAP 映像除了可存取基底 RHEL 套件外，也可以存取 SAP 套件。

### <a name="rhui-update-behavior"></a>RHUI 更新行為
根據預設，連線至 RHUI 的 RHEL 映像將會在執行 `yum update` 時，更新至最新的 RHEL 次要版本。 此行為表示，如果 RHEL 7.4 VM 上執行了 `yum update` 作業，便可能會升級至 RHEL 7.7。 這是 RHUI 的設計。 不過，您可以藉由從一般 RHEL 存放庫切換到[擴充更新支援 (EUS) 存放庫](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)來減輕此升級行為。

## <a name="next-steps"></a>後續步驟
* 深入了解 [Azure 上的 RHEL 映像](./redhat-images.md)
* 深入了解 [Red Hat 更新基礎結構](./redhat-rhui.md)
* 深入了解 [Red Hat Gold 映像 (`rhel-byos`) 供應項目](./byos.md)
