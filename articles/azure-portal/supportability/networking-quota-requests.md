---
title: 網路限制增加 |Microsoft Docs
description: 增加網路限制
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76547714"
---
# <a name="networking-limit-increase"></a>增加網路限制

使用[Azure 入口網站](https://portal.azure.com)來增加您的網路配額。

若要在 Azure 入口網站中查看目前的網路使用量和配額，請開啟您的訂用帳戶，然後選取 [**使用量 + 配額**]。 您也可以使用下列選項來查看您的網路使用量和限制。

* [使用方式 CLI](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [網路使用方式 API](/rest/api/virtualnetwork/virtualnetworks/listusage)

您可以使用 [說明 **+ 支援**] 或在入口網站中的 [使用方式 **+ 配額**] 來要求增加。

> [!Note]
> 若要變更**公用 IP 首碼**的預設大小，請從下拉式清單中選取 [**最小公用 Ip 網際網路前置長度**]。

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>使用說明 + 支援在訂用帳戶層級增加要求網路配額

請依照下列指示，使用 Azure 入口網站中的 [說明 **+ 支援**] 來建立支援要求。

1. 登入[Azure 入口網站](https://portal.azure.com)，然後從 [Azure 入口網站] 功能表中選取 [說明 **+ 支援**]，或搜尋並選取 [說明 **+ 支援**]。

    ![說明 + 支援](./media/networking-quota-request/help-plus-support.png)

1. 選取 [新增支援要求]****。

    ![新增支援要求](./media/networking-quota-request/new-support-request.png)

1. 針對 [**問題類型**]，選擇 [**服務和訂用帳戶限制（配額）**]。

    ![從 [問題類型] 下拉式清單中選取訂用帳戶限制](./media/networking-quota-request/select-quota-issue-type.png)

1. 選取需要增加配額的訂用帳戶。

    ![選取訂用帳戶 newSR](./media/networking-quota-request/select-subscription-support-request.png)

1. 在 [**配額類型**] 底下，選取 [**網路**]。 選取 **[下一步：解決方案]**。

    ![選取配額類型](./media/networking-quota-request/select-quota-type-network.png)

1. 在 [**問題詳細資料**] 中，選取 [**提供詳細資料**] 並填入其他資訊，以協助處理您的要求。

    ![提供詳細資料](./media/networking-quota-request/provide-details-link.png)

1. 在 [**配額詳細資料**] 面板中，選取部署模型、位置，以及要包含在要求中的資源。

    ![配額詳細資料 DM](./media/networking-quota-request/quota-details-network.png)

1. 輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請從 [**資源**] 功能表中取消選取資源，或選取 [捨棄 "x"] 圖示。 輸入每個資源的配額之後，請選取 [**儲存並繼續**] 以繼續進行支援要求建立。

    ![新限制](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>使用使用量 + 配額，在訂用帳戶層級增加要求網路配額

遵循這些指示，在 Azure 入口網站中使用 [**使用量 + 配額**] 來建立支援要求。

1. 從https://portal.azure.com中，搜尋並選取 [**訂閱**]。

    ![訂閱](./media/networking-quota-request/search-for-suscriptions.png)

1. 選取需要增加配額的訂用帳戶。

    ![選取訂閱](./media/networking-quota-request/select-subscription-change-quota.png)

1. 選取**使用量 + 配額**

    ![選取使用量和配額](./media/networking-quota-request/select-usage-plus-quotas.png)

1. 在右上角，選取 [**要求增加**]。

    ![要求增加配額](./media/networking-quota-request/request-increase-from-subscription.png)

1. 遵循在訂用帳戶[層級的要求網路配額增加](#request-networking-quota-increase-at-subscription-level-using-help--support)中步驟3開始的步驟。

## <a name="about-networking-limits"></a>關於網路限制

若要深入瞭解網路限制，請參閱限制頁面的[網路功能一節](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)，或我們的網路限制常見問題。
