---
title: 網路限制增加 |微軟文檔
description: 增加網路限制
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547714"
---
# <a name="networking-limit-increase"></a>增加網路限制

使用[Azure 門戶](https://portal.azure.com)增加網路配額。

要查看 Azure 門戶中的當前網路使用方式和配額，請打開訂閱，然後選擇 **"使用方式 " 和配額**。 您還可以使用以下選項查看網路使用方式和限制。

* [使用 CLI](/cli/azure/network#az-network-list-usages)
* [電源外殼](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [網路使用方式 API](/rest/api/virtualnetwork/virtualnetworks/listusage)

您可以通過在門戶中使用**説明和支援**或 **"使用方式 " 配額**請求增加。

> [!Note]
> 要更改公共 IP**首碼**的預設大小，請從下拉清單中選擇**最小公共 IP 網路前置長度**。

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>使用説明和支援在訂閱級別請求網路配額增加

按照以下說明，在 Azure 門戶中使用説明 **+ 支援**來創建支援請求。

1. 登錄到[Azure 門戶](https://portal.azure.com)，然後從 Azure 門戶功能表中選擇 **"説明 + 支援**"，或搜索並選擇 **"説明 + 支援**"。

    ![說明 + 支援](./media/networking-quota-request/help-plus-support.png)

1. 選取 [新增支援要求]****。

    ![新增支援要求](./media/networking-quota-request/new-support-request.png)

1. 對於 **"問題"類型**，請選擇 **"服務和訂閱限制"（配額）。**

    ![從問題類型下拉清單中選擇訂閱限制](./media/networking-quota-request/select-quota-issue-type.png)

1. 選取需要增加配額的訂用帳戶。

    ![選擇訂閱新 SR](./media/networking-quota-request/select-subscription-support-request.png)

1. 在**配額類型**下，選擇 **"網路**"。 選擇 **"下一步"：解決方案**。

    ![選擇配額類型](./media/networking-quota-request/select-quota-type-network.png)

1. 在**問題詳細資訊**中，選擇 **"提供詳細資訊**"並填寫其他資訊以説明處理您的請求。

    ![提供詳細資訊](./media/networking-quota-request/provide-details-link.png)

1. 在 **"配額詳細資訊"** 面板中，選擇部署模型、位置和要包含在請求中的資源。

    ![配額詳細資訊 DM](./media/networking-quota-request/quota-details-network.png)

1. 輸入您想要對訂用帳戶採取的新限制。 要刪除行，請從 **"資源"** 功能表中取消選擇資源，或選擇丟棄的"x"圖示。 輸入每個資源的配額後，選擇 **"保存"並繼續**創建支援請求。

    ![新限制](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>使用使用方式和配額在訂閱級別請求網路配額增加

請按照這些說明在 Azure 門戶中使用 **"使用方式 + 配額**"來創建支援請求。

1. 從https://portal.azure.com中 搜索 並選擇 **"訂閱**"。

    ![訂用帳戶](./media/networking-quota-request/search-for-suscriptions.png)

1. 選取需要增加配額的訂用帳戶。

    ![選取訂閱](./media/networking-quota-request/select-subscription-change-quota.png)

1. 選擇**使用方式和配額**

    ![選取使用量和配額](./media/networking-quota-request/select-usage-plus-quotas.png)

1. 在右上角，選擇 **"請求增加**"。

    ![要求增加配額](./media/networking-quota-request/request-increase-from-subscription.png)

1. 按照在[訂閱級別請求網路配額增加](#request-networking-quota-increase-at-subscription-level-using-help--support)中的步驟 3 開始的步驟。

## <a name="about-networking-limits"></a>關於網路限制

要瞭解有關網路限制的更多資訊，請參閱限制頁面[的網路部分](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)或我們的網路限制常見問題解答。
