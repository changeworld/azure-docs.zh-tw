---
title: 支援搭配 Azure 備份使用 Azure Site Recovery
description: 提供如何搭配使用 Azure Site Recovery 和 Azure 備份的總覽。
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135185"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>支援搭配 Azure 備份使用 Site Recovery

本文摘要說明搭配[Azure 備份服務](../backup/backup-overview.md)使用[Site Recovery 服務](site-recovery-overview.md)的支援。

**動作** | **Site Recovery 支援** | **詳細資料**
--- | --- | ---
**一起部署服務** | 支援 | 服務是互通的，而且可以同時設定。
**檔案備份/還原** | 支援 | 當 VM 的備份和複寫已啟用，且已執行備份時，在來源端 Vm 或 Vm 群組上還原檔案並不會有任何問題。 複寫會如往常般繼續，且複寫健全狀況不會有任何變更。
**磁片還原** | 目前不支援 | 如果您還原已備份的磁片，您需要再次停用並重新啟用 VM 的複寫。
**VM 還原** | 目前不支援 | 如果您還原 VM 或 vm 群組，您必須停用並重新啟用 VM 的複寫。  


