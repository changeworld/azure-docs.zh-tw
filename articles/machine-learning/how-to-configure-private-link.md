---
title: " (預覽版設定私人端點) "
titleSuffix: Azure Machine Learning
description: 使用 Azure Private Link 從虛擬網路安全地存取您的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/03/2020
ms.openlocfilehash: 83927c9df9a4f1a6ab32c15c481898ec68f53c4c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898146"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>設定 Azure Machine Learning 工作區的 Azure Private Link (預覽) 

在本檔中，您將瞭解如何搭配使用 Azure Private Link 與您的 Azure Machine Learning 工作區。 如需針對 Azure Machine Learning 設定虛擬網路的相關資訊，請參閱 [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md)

> [!IMPORTANT]
> 使用 Azure Private Link 搭配 Azure Machine Learning 工作區目前處於公開預覽狀態。 這項功能僅適用于 **美國東部**、 **美國中南部** 和 **美國西部 2** 區域。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Private Link 可讓您使用私人端點連接到工作區。 私人端點是虛擬網路內的一組私人 IP 位址。 然後，您可以將工作區的存取許可權制為只在私人 IP 位址上進行。 Private Link 有助於降低資料遭到外泄的風險。 若要深入了解私人端點，請參閱 [Azure Private Link](/azure/private-link/private-link-overview) 一文。

> [!IMPORTANT]
> Azure Private Link 不會影響 Azure 控制平面 (管理作業) 例如刪除工作區或管理計算資源。 例如，建立、更新或刪除計算目標。 這些作業會正常地在公用網際網路上執行。 資料平面作業（例如使用 Azure Machine Learning studio、Api (包括) 的已發佈管線），或 SDK 使用私人端點。
>
> 如果您使用 Mozilla Firefox，您可能會遇到嘗試存取工作區私人端點的問題。 此問題可能與 Mozilla 的 HTTPS DNS 相關。 我們建議使用 Google Chrome Microsoft Edge 作為因應措施。

> [!TIP]
> Azure Machine Learning 計算實例可以與工作區和私人端點搭配使用。 這項功能目前在 **美國東部**、 **美國中南部** 和 **美國西部 2** 區域處於公開預覽狀態。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>建立使用私人端點的工作區

> [!IMPORTANT]
> 目前，我們只支援在建立新的 Azure Machine Learning 工作區時啟用私人端點。

的範本 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) 可以用來建立具有私人端點的工作區。

如需使用此範本的詳細資訊（包括私人端點），請參閱 [使用 Azure Resource Manager 範本建立 Azure Machine Learning 的工作區](how-to-create-workspace-template.md)。

## <a name="using-a-workspace-over-a-private-endpoint"></a>使用私人端點上的工作區

因為僅允許從虛擬網路對工作區進行通訊，使用該工作區的任何開發環境都必須是虛擬網路的成員。 例如，虛擬網路中的虛擬機器。

> [!IMPORTANT]
> 為了避免連線暫時中斷，Microsoft 建議在啟用 Private Link 後，在連線到工作區的電腦上清除 DNS 快取。 

如需 Azure 虛擬機器的詳細資訊，請參閱 [虛擬機器檔](/azure/virtual-machines/)。


## <a name="next-steps"></a>下一步

如需保護 Azure Machine Learning 工作區安全的詳細資訊，請參閱 [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md) 文章。
