---
title: Linux 上的 SQL Server 虛擬機器常見問題 |Microsoft Docs
description: 本文提供有關執行 Linux 上的 SQL Server 虛擬機器之常見問題的解答。
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792543"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Linux 上的 SQL Server 虛擬機器的常見問題
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

本文提供有關執行 [Linux 上的 SQL Server 虛擬機器](sql-server-on-linux-vm-what-is-iaas-overview.md)的一些常見問題的解答。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> 映像

1. **可用的 SQL Server 虛擬機器資源庫映像有哪些？**

   Azure 會在 Linux 和 Windows 的所有版本上，針對所有支援的 SQL Server 主要版本，維護虛擬機器 (VM) 映射。 如需詳細資訊，請參閱完整的 [Linux VM 映像](sql-server-on-linux-vm-what-is-iaas-overview.md#create)和 [Windows VM 映像](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)清單。

1. **現有的 SQL Server 虛擬機器資源庫映像是否已更新？**

   每隔兩個月，系統會以最新的 Linux 和 Windows 更新來更新虛擬機器資源庫中的 SQL Server 映像。 若為 Linux 映像，這包括最新的系統更新。 針對 Windows 映像，這包括 Windows Update 中標示為重要的更新，包括重要 SQL Server 安全性更新和 service pack。 適用於 Linux 和 Windows 的 SQL Server 累計更新會以不同的方式處理。 針對 Linux，SQL Server 累計更新也會包含在重新整理中。 但此時，系統不會以 SQL Server 或 Windows Server 累計更新來更新 Windows VM。

1. **還安裝了哪些相關的 SQL Server 套件？**

   若要查看 Linux 上的 SQL Server Vm 上預設安裝的 SQL Server 套件，請參閱 [已安裝的套件](sql-server-on-linux-vm-what-is-iaas-overview.md#packages)。

1. **是否可以從資源庫中移除 SQL Server 虛擬機器映像？**

   是。 Azure 只會為每個主要版本維護一個映像。 例如，發行新的 SQL Server Service Pack 後，Azure 會將新的映像新增至該 Service Pack 的資源庫。 前一個 Service Pack 的 SQL Server 映像會立即從 Azure 入口網站中移除。 不過，在接下來三個月仍可用於從 PowerShell 佈建。 三個月之後，便無法再使用前一個 Service Pack 映像。 如果 SQL Server 版本在達到其生命週期結尾時就不提供支援，也適用此移除原則。

## <a name="creation"></a>建立

1. **如何? 使用 SQL Server 建立 Linux 虛擬機器？**

   最簡單的解決方案是建立包含 SQL Server 的 Linux 虛擬機器。 如需註冊 Azure 並從入口網站建立 SQL Server VM 的教學課程，請參閱 Azure 入口網站中的布建 [執行 SQL Server 的 Linux 虛擬機器](sql-vm-create-portal-quickstart.md)。 您也可以選擇使用免費授權版本 (Developer 或 Express) 或重複使用內部部署授權，在 VM 上手動安裝 SQL Server。 如果您自備授權，必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility)。

1. **我為什麼不能使用有消費限制的 Azure 訂用帳戶來佈建 RHEL 或 SLES SQL Server VM？**

   RHEL 與 SLES 虛擬機器需要沒有消費限制的訂用帳戶，以及與該訂用帳戶相關聯的已驗證付款方式 (通常為信用卡)。 如果您佈建 RHEL 或 SLES VM，但未移除消費限制，您的訂用帳戶將會被停用，而且所有 VM/服務都會停止。 如果您進入此狀態，若要重新啟用該訂用帳戶，請[移除消費限制](https://account.windowsazure.com/subscriptions)。 將會還原您目前計費週期剩餘的信用額度，但如果您選擇重新開始並繼續執行 RHEL 或 SLES VM 映像，則會向您的信用卡收取其額外費用。

## <a name="licensing"></a>授權

1. **如何在 Azure VM 上安裝 SQL Server 授權版本？**

   首先，建立只有 Linux OS 的虛擬機器。 然後針對您的 Linux 發行版本執行 [SQL Server 安裝步驟](/sql/linux/sql-server-linux-setup#platforms)。 除非您安裝其中一個免費授權的 SQL Server 版本，否則您也必須要有 SQL Server 授權與 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。

1. **是否有適用於 SQL Server 的自備授權 (BYOL) Linux 虛擬機器映像？**

   目前，沒有適用於 SQL Server 的 BYOL Linux 虛擬機器映像。 不過，您可以在僅有 Linux 的 VM 上手動安裝 SQL Server，如先前的問題中所述。

1. **如果是從其中一個隨用隨付資源庫映像建立，可以將 VM 變更為使用自己的 SQL Server 授權嗎？**

   否。 您不能從以秒鐘計費授權切換為使用您自己的授權。 您必須建立新的 Linux VM，安裝 SQL Server，然後移轉資料。 如需自備授權的詳細資訊，請參閱上一個問題。

## <a name="administration"></a>系統管理

1. **我可以使用 SQL Server Management Studio (SSMS) 來管理執行 SQL Server 的 Linux 虛擬機器嗎？**

   是，但 SSMS 目前為僅限 Windows 的工具。 您必須從 Windows 電腦遠端連線，才能搭配執行 SQL Server 的 Linux Vm 使用 SSMS。 在 Linux 本機上，新的 [mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf) 工具可以執行許多管理工作。 如需跨平台的資料庫管理工具，請參閱 [Azure Data Studio](/sql/azure-data-studio/what-is)。

1. **可以從 SQL Server VM 完全移除 SQL Server 嗎？**

   可以，不過如 [SQL Server Azure VM 的定價指引](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json) 中所述，您仍需繼續支付 SQL Server VM 的費用。 如果您不再需要 SQL Server，則可以部署新的虛擬機器，並將資料和應用程式移轉到新的虛擬機器。 接著，您可以移除 SQL Server 虛擬機器。

## <a name="updating-and-patching"></a>更新和修補

1. **如何將 Azure VM 中的 SQL Server 升級至新版本？**

   目前，在 Azure VM 中執行的 SQL Server 不提供任何就地升級。 因此，請建立具有所需 SQL Server 版本的 Azure 虛擬機器，然後使用[標準資料移轉技術](/sql/linux/sql-server-linux-migrate-overview)，將資料庫移轉到新的伺服器。

## <a name="general"></a>一般

1. **Azure Vm 上是否支援 SQL Server 高可用性解決方案？**

   目前沒有。 Always On 可用性群組和容錯移轉叢集都需要 Linux 中的群集解決方案，例如 Pacemaker。 SQL Server 支援的 Linux 發行版本不支援其在雲端中的高可用性附加元件。

## <a name="resources"></a>資源

**Linux VM** ：

* [Linux VM 上的 SQL Server 概觀](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [在 Linux VM 上布建 SQL Server](sql-vm-create-portal-quickstart.md)
* [Linux 上的 SQL Server 文件](/sql/linux/sql-server-linux-overview) \(機器翻譯\)

**Windows VM** ：

* [Windows VM 上的 SQL Server 概觀](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [在 Windows VM 上布建 SQL Server](../windows/sql-vm-create-portal-quickstart.md)
* [常見問題集 (Windows)](../windows/frequently-asked-questions-faq.md)