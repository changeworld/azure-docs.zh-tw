---
title: Azure 網路監看員中的網路設定診斷簡介 |Microsoft Docs
description: 本頁提供網路監看員-網路設定診斷的總覽
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: ad60b0be8a9a5341c71d760f98c0db84a72763c2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951514"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Azure 網路監看員中的網路設定診斷簡介

網路設定診斷工具可協助客戶瞭解哪些流量會在您的 Azure 虛擬網路中被允許或拒絕，以及詳細的偵錯工具資訊。 它可協助您瞭解 NSG 規則是否已正確設定。 

## <a name="pre-requisites"></a>必要條件
若要使用網路設定診斷，必須在您的訂用帳戶中啟用網路監看員。 請參閱建立要啟用 [的 Azure 網路](./network-watcher-create.md) 監看員實例。

## <a name="background"></a>背景

- 您在 Azure 中的資源會透過虛擬網路 (Vnet) 和子網進行連線。 您可以使用網路安全性群組 (NSG) 來管理這些 Vnet 和子網的安全性。
- NSG 包含安全性規則的清單，可允許或拒絕其所連接之資源的網路流量。 Nsg 可以與子網、個別 Vm 或個別網路介面相關聯， (Nic) 連接到 Vm。 
- 您網路中的所有流量都會使用適用 NSG 中的規則進行評估。
- 規則是根據優先順序數位（從最低到最高）進行評估 

## <a name="how-does-network-configuration-diagnostic-work"></a>網路設定診斷如何運作？ 

針對給定的流程，NCD 工具會執行流程的模擬，並傳回流程是否允許 (或拒絕) ，以及允許/拒絕流程之規則的詳細資訊。  客戶必須提供流量的詳細資料，例如來源、目的地、通訊協定等。此工具會傳回是否允許或拒絕流量、針對指定流程評估的 NSG 規則，以及每個規則的評估結果。

## <a name="next-steps"></a>後續步驟

透過其他介面使用網路設定診斷
 - [REST API](/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic?view=azps-4.6.1)
 - [Azure CLI](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_run_configuration_diagnostic)