---
title: 使用 Azure 入口網站來瞭解 Azure 虛擬機器的維護控制
description: 瞭解如何使用維護控制來控制將維護套用至 Azure Vm 的時機。
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 4b9dec0fe684e002fadbac2db375c354db2b6d01
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981165"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>使用維護控制來管理平臺更新 

使用維護控制，管理不需要重新開機的平臺更新。 Azure 會經常更新其基礎結構，以改善可靠性、效能、安全性或推出新功能。 大部分的更新對使用者而言是透明的。 某些敏感性工作負載（例如遊戲、媒體串流處理和財務交易）無法容忍甚至幾秒的 VM 凍結或中斷連線以進行維護。 維護控制可讓您選擇等候平臺更新，並將它們套用到35天的滾動時段內。 

維護控制可讓您決定何時要將更新套用至隔離的 Vm 和 Azure 專用主機。

使用維護控制，您可以：
- 批次更新為一個更新套件。
- 等候最多35天的時間套用更新。 
- 藉由設定維護排程或使用 [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)，將平臺更新自動化。
- 維護設定可以跨訂用帳戶和資源群組運作。 

## <a name="limitations"></a>限制

- Vm 必須位於 [專用主機](./dedicated-hosts.md)上，或使用 [隔離的 VM 大小](isolation.md)來建立。
- 如果已宣告維護排程，則至少必須是2小時。
- 在35天后，將會自動套用更新。
- 使用者必須擁有 **資源參與者** 存取權。

## <a name="management-options"></a>管理選項

您可以使用下列任何選項來建立和管理維護設定：

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure 入口網站](maintenance-control-portal.md)

如需 Azure Functions 範例，請參閱 [使用維護控制排程維護更新和 Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱 [維護和更新](maintenance-and-updates.md)。
