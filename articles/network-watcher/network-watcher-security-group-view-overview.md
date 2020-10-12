---
title: Azure 網路監看員中的有效安全性規則的簡介 |Microsoft Docs
description: 本頁提供網路監看員-有效安全性規則查看功能的總覽
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: f4c601184a060c3dfc4f033bcf983bf773f7167f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87022645"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Azure 網路監看員中的有效安全性規則查看簡介

網路安全性群組會在子網路層級或 NIC 層級產生關聯。 若在子網路層級產生關聯，它會套用至子網路中的所有 VM 執行個體。 [有效的安全性規則] view 會傳回虛擬機器的 NIC 和子網層級相關聯的所有已設定 Nsg 和規則，提供設定的深入解析。 此外，VM 中的每個 NIC 也會傳回有效的安全性規則。 您可以使用有效的安全性規則視圖，來評估 VM 是否有網路弱點，例如開啟的埠。 您也可以根據[所設定的安全性規則和核准的安全性規則之間的比較](network-watcher-nsg-auditing-powershell.md)，驗證網路安全性群組是否如預期般運作。

更大範圍的使用案例為安全性合規性與稽核。 您可以定義一組規定的安全性規則，以做為控管組織安全性的模型。 藉由比較網路中每個 VM 的規定規則和有效規則，即可以程式設計方式實作定期合規性稽核。

會顯示每個網路介面的入口網站規則，並依輸入與輸出分組。 這可讓您簡潔地了解套用至虛擬機器的規則。 檢視畫面中提供了 [下載] 按鈕，以方便您輕鬆地下載所有安全性規則 (不論其標籤為何) 到 CSV 檔案中。

![安全性群組檢視][1]

您可以選取規則，隨即會開啟新的刀鋒視窗，顯示網路安全性群組和來源與目的地前置詞。 從這個刀鋒視窗中，您可以直接瀏覽至網路安全性群組資源。

![向下鑽研][2]

### <a name="next-steps"></a>接下來的步驟

您也可以透過下列其他方法使用 *有效的安全性群組* 功能：
* [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azeffectivenetworksecuritygroup?view=azps-4.4.0)
* [Azure CLI](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-list-effective-nsg)

瀏覽[使用 PowerShell 稽核網路安全性群組設定](network-watcher-nsg-auditing-powershell.md)，以了解如何稽核網路安全性群組設定

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









