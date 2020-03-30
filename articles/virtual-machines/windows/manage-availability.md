---
title: 管理 Azure 中 Windows VM 的可用性
description: 了解如何使用多部虛擬機器，以確保 Azure 中 Windows 應用程式的高可用性。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae5d60f77319a6590807ae0b18a0c07c116e128b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267439"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>管理 Azure 中 Windows 虛擬機器的可用性 

了解如何設定及管理多部虛擬機器，以確保 Azure 中 Windows 應用程式的高可用性。 您也可以 [管理 Linux 虛擬機器的可用性](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>後續步驟
若要深入了解如何對虛擬機器進行負載平衡，請參閱 [對虛擬機器進行負載平衡](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

查看在 IaaS 中的 SQL Server 上運行 N 層應用程式的參考體系結構

* [Azure 上具有 SQL Server 的 Windows 多層式架構 (N-tier) 應用程式](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [在多個 Azure 區域中執行多層式架構 (N-Tier) 應用程式以獲得高可用性](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
