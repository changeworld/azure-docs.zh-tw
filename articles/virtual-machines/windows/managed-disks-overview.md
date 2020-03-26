---
title: Windows VM 的 Azure 磁碟儲存體概觀
description: 使用 Azure Windows VM 時為您處理儲存體帳戶的 Azure 受控磁碟概觀
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b2c2c1289a40d63b2f396ee59000c3aedb14c3d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75460040"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure 受控磁碟簡介

Azure 受控磁碟是由 Azure 管理並與 Azure 虛擬機器搭配使用的區塊層級儲存體磁碟區。 受控磁碟就像是內部部署伺服器中虛擬化的實體磁碟。 使用受控磁碟時，您只需要指定磁碟大小、磁碟類型，以及物件磁碟。 佈建磁碟後，Azure 就會處理其餘事項。

這些可用的磁碟類型為 Ultra 磁碟、進階固態硬碟 (SSD)、標準 SSD 和標準硬碟 (HDD)。 如需每種個別磁碟類型的相關資訊，請參閱[選取適用於 IaaS VM 的磁碟類型](disks-types.md)。

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [選取適用於 IaaS VM 的磁碟類型](disks-types.md)
