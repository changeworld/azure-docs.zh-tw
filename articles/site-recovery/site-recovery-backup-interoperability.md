---
title: 支援搭配使用 Azure Site Recovery 與 Azure 備份
description: 概要說明如何搭配使用 Azure Site Recovery 和 Azure 備份。
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135185"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>支援搭配使用 Site Recovery 與 Azure 備份

本文摘要說明將 [Site Recovery 服務](site-recovery-overview.md) 與 [Azure 備份服務](../backup/backup-overview.md)搭配使用的支援。

**動作** | **Site Recovery 支援** | **詳細資料**
--- | --- | ---
**一起部署服務** | 支援 | 服務可互通，並可一起設定。
**檔案備份/還原** | 支援 | 啟用 VM 的備份和複寫並進行備份時，在來源端 Vm 或 Vm 群組上還原檔案沒有任何問題。 複寫健康狀態不會變更，因此複寫會照常繼續。
**磁片還原** | 沒有目前的支援 | 如果您還原備份的磁片，則需要再次停用並重新啟用 VM 的複寫。
**VM 還原** | 沒有目前的支援 | 如果您還原 VM 或 vm 群組，您需要停用 VM 的複寫，然後再重新啟用。  


