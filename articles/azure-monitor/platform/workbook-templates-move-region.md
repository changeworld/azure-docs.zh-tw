---
title: Azure 監視器活頁簿範本-移動區域
description: 如何將活頁簿範本移至不同的區域
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: f37550d914cf7efb0c75ed3dfa8854e1ec7be7e0
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875615"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>將 Azure 活頁簿範本移至另一個區域

本文說明如何將 Azure 活頁簿範本資源移至不同的 Azure 區域。 您可能會因為許多原因而將資源移至另一個區域。 例如，若要利用新的 Azure 區域，只部署特定區域中提供的功能或服務，以符合內部原則和治理需求，或為了回應容量規劃需求。

目前沒有可建立活頁簿範本資源的入口網站 UI，唯一建立它們的方式是透過 [Azure Resource Manager 範本 (ARM 範本) 部署](./workbooks-automate.md)。 因此，移動範本最簡單的方式是重複使用先前的 ARM 範本，並將其更新為部署到新的區域。

## <a name="prerequisites"></a>先決條件

* 確定目的地區域中支援活頁簿範本。

## <a name="prepare"></a>準備

* 識別活頁簿範本先前使用的 ARM 範本。

## <a name="move"></a>移動

1. 更新先前使用的範本，以參考新的區域。

   > [!NOTE]
   > 您可能需要使用新的活頁簿範本名稱，以避免任何重複的名稱。

2. 透過 ARM 範本部署部署更新的範本，以在所需的區域中建立新的活頁簿範本。

## <a name="verify"></a>Verify

使用 Azure 活頁簿流覽 UI 來尋找新部署的活頁簿範本。 確定位置是目標位置。

## <a name="clean-up"></a>清理

在新區域中建立活頁簿範本之後，請刪除先前區域中原始的活頁簿範本。
1. 在 Azure 活頁簿流覽 UI 中尋找活頁簿範本。
2. 選取要刪除的活頁簿範本。
3. 選取 [刪除] 命令。

如果您重新命名活頁簿範本以將它匯入至新的區域，您可以使用 Azure 活頁簿範本資源檢視中的 [重新命名] 命令，在刪除原始專案之後，將活頁簿範本重新命名為先前的名稱。

## <a name="next-steps"></a>後續步驟

需要移動活頁簿而不是範本嗎？ 瞭解如何 [將 Azure 活頁簿移至另一個區域](./workbooks-move-region.md)。

