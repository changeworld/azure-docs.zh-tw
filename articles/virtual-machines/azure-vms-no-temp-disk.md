---
title: 常見問題-沒有本機暫存磁片的 Azure VM 大小
description: 本文提供常見問題的解答 (常見問題) 有關沒有本機暫存磁片的 Microsoft Azure VM 大小。
author: brbell
ms.service: virtual-machines
ms.topic: conceptual
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 30587fac7d7be37d7595a78502b7999adee9a30f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665305"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>沒有本機暫存磁片的 Azure VM 大小 
本文提供常見問題的解答 (常見問題) 有關沒有本機暫存磁片的 Azure VM 大小 (亦即沒有本機暫存磁片) 。 如需這些 VM 大小的詳細資訊，請參閱 [Dv4 和 Dsv4 系列 (一般用途工作負載的規格) ](dv4-dsv4-series.md) 或 [Ev4 和 Esv4 系列 (記憶體優化工作負載) 的規格 ](ev4-esv4-series.md)。

## <a name="what-does-no-local-temp-disk-mean"></a>沒有本機暫存磁片的意義為何？ 
傳統上，我們有 VM 大小 (例如 Standard_D2s_v3、Standard_E48_v3) ，其中包含一個小型本機磁片 (例如 D：磁片磁碟機) 。 現在有了這些新的 VM 大小，該小型本機磁片已不存在;不過，您仍然可以附加標準 HDD、進階 SSD 或 Ultra SSD。

## <a name="what-if-i-still-want-a-local-temp-disk"></a>如果我還想要本機暫存磁片，該怎麼辦？
如果您的工作負載需要本機暫存磁片，我們也提供新的 [Ddv4 和 Ddsv4](ddv4-ddsv4-series.md) 或 [Edv4 和 Edsv4](edv4-edsv4-series.md) VM 大小。 相較于先前的 v3 大小，這些大小提供50% 的較大暫存磁片。

> [!NOTE]
> 本機暫存磁片不是持續性;若要確保您的資料持續存在，請使用標準 HDD、進階 SSD 或 Ultra SSD 選項。 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>這些新的 VM 大小和一般用途的 Dv3/Dsv3 或我所用的記憶體優化 Ev3/Esv3 VM 大小有何差異？ 
| 使用本機暫存磁片的 v3 VM 系列   | 使用本機暫存磁片的新 v4 系列 | 沒有本機暫存磁片的新 v4 系列 |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>是否可以將具有本機暫存磁片的 VM 大小調整為沒有本機暫存磁片的 VM 大小？  
否。 唯一允許調整大小的組合如下： 

1. 具有本機暫存磁片的 VM () > VM (與本機暫存磁片) ;和 
2. 沒有本機暫存磁片的 VM () > VM (沒有本機暫存磁片) 。 

> [!NOTE]
> 如果映射相依于資源磁片，或本機暫存磁片上有分頁檔或 cloud-init，則無磁片映射將無法運作，而是使用「磁片替代」替代選項。 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>這些 VM 大小是否支援 Linux 和 Windows 作業系統 (OS) ？
是。

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>這會中斷我的自訂腳本、自訂映射或本機暫存磁片上有暫存檔案或分頁檔案的作業系統映射？
如果自訂 OS 映射指向本機暫存磁片，則映射可能無法正確地使用此無磁片大小。

## <a name="have-questions-or-feedback"></a>有任何問題或意見嗎？
填寫意見反應 [表單]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u)。 

## <a name="next-steps"></a>後續步驟 
在本檔中，您已深入瞭解與沒有本機暫存磁片的 Azure Vm 相關的最常見問題。 如需這些 VM 大小的詳細資訊，請參閱下列文章：

- [Dv4 和 Dsv4 系列 (一般用途工作負載的規格) ](dv4-dsv4-series.md)
- [Ev4 和 Esv4 系列 (記憶體優化工作負載的規格) ](ev4-esv4-series.md)
