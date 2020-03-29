---
title: 支援將 Azure 網站恢復與 Azure 備份一起使用
description: 概述了如何一起使用 Azure 網站恢復和 Azure 備份。
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376225"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>支援將網站恢復與 Azure 備份一起使用

本文總結了對將[網站恢復服務](site-recovery-overview.md)與[Azure 備份服務](https://docs.microsoft.com/azure/backup/backup-overview)一起使用的支援。

**動作** | **Site Recovery 支援** | **詳細資料**
--- | --- | ---
**一起部署服務** | 支援 | 服務是可交互操作的，可以一起配置。
**檔案備份/還原** | 支援 | 為 VM 啟用備份和複製並執行備份時，還原源端 VM 或 VM 組上的檔沒有問題。 複製照常進行，複製運行狀況沒有變化。
**磁片恢復** | 無當前支援 | 如果還原備份的磁片，則需要再次禁用並重新啟用 VM 的複製。
**VM 還原** | 無當前支援 | 如果還原 VM 或 VM 組，則需要禁用並重新啟用 VM 的複製。  


