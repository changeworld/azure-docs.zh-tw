---
title: 使用 Azure 入口網站來瞭解 Azure 虛擬機器的維護控制
description: 瞭解如何控制使用維護控制將維護套用至 Azure Vm 的時機。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 4c5e30d0607db2d529ae41ebab6dc82e925ff2a8
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82139195"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>使用維護控制來管理平臺更新 

使用維護控制來管理不需要重新開機的平臺更新。 Azure 會經常更新其基礎結構，以改善可靠性、效能、安全性或啟動新功能。 大部分的更新對使用者而言是透明的。 某些敏感的工作負載（例如遊戲、媒體串流處理和財務交易）無法容忍虛擬機器甚至幾秒的時間，就會凍結或中斷連接以進行維護。 維護控制可讓您選擇是否要等候平臺更新，並將它們套用到35天的輪流時段內。 

維護控制可讓您決定何時要將更新套用到隔離的 Vm 和 Azure 專用主機。

有了維護控制，您可以：
- 批次更新為一個更新套件。
- 請等候35天以套用更新。 
- 使用 Azure Functions 自動進行維護視窗的平臺更新。
- 維護設定適用于訂用帳戶和資源群組。 

## <a name="limitations"></a>限制

- Vm 必須位於[專用主機](./linux/dedicated-hosts.md)上，或使用[隔離的 VM 大小](./linux/isolation.md)建立。
- 35天后，將會自動套用更新。
- 使用者必須擁有**資源參與者**存取權。

## <a name="management-options"></a>管理選項

您可以使用下列任何選項來建立和管理維護設定：

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure 入口網站](maintenance-control-portal.md)

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱[維護和更新](maintenance-and-updates.md)。
