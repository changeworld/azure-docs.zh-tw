---
title: 使用 Azure 入口網站管理 Azure DDoS Protection Standard
description: 瞭解如何使用 Azure DDoS 保護標準來緩和攻擊。
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: fc60ca462a2891cc022847e056e32239f2675f70
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094569"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>快速入門：建立和設定 Azure DDoS 保護 Standard

使用 Azure 入口網站 Azure DDoS 保護 Standard 入門。 

DDoS 保護計劃會定義一組跨訂用帳戶且已啟用標準 DDoS 保護的虛擬網路。 您可以為組織設定一個 DDoS 保護計劃，然後將來自多個訂用帳戶的虛擬網路連結至該相同計劃。 

在本快速入門中，您將建立 DDoS 保護計劃，並將它連結至虛擬網路。 

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在 https://portal.azure.com 登入 Azure 入口網站。 確定您的帳戶已指派給「 [網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 」角色，或指派給 [自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ，該角色已獲指派 [操作指南] [許可權](manage-permissions.md)中所列的適當動作。

## <a name="create-a-ddos-protection-plan"></a>建立 DDoS 保護計劃

1. 選取 Azure 入口網站左上角的 [ **建立資源** ]。
2. 搜尋「 *DDoS* 」一詞。 當 **DDoS 保護計劃** 出現在搜尋結果中時，請加以選取。
3. 選取 [建立]。
4. 輸入或選取下列值，然後選取 [ **建立** ：

    |設定        |值                                              |
    |---------      |---------                                          |
    |名稱           | 輸入 _MyDdosProtectionPlan_ 。                     |
    |訂用帳戶   | 選取您的訂用帳戶。                         |
    |資源群組 | 選取 [ **建立新** 的]，然後輸入 _MyResourceGroup_ 。|
    |位置       | 請輸入 _美國東部_ 。                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>為虛擬網路啟用 DDoS 保護

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>針對新的虛擬網路啟用 DDoS 保護

1. 選取 Azure 入口網站左上角的 [ **建立資源** ]。
2. 選取 [ **網路** ]，然後選取 [ **虛擬網路** ]。
3. 輸入或選取下列值，接受其餘的預設值，然後選取 [ **建立** ]：

    | 設定         | 值                                           |
    | ---------       | ---------                                       |
    | 名稱            | 輸入 _MyVnet_ 。                                 |
    | 訂用帳戶    | 選取您的訂用帳戶。                                    |
    | 資源群組  | 選取 [ **使用現有** 的]，然後選取 [ **MyResourceGroup** ] |
    | 位置        | 輸入 _美國東部_                                                    |
    | Azure 標準 DDoS 保護 | 選取 [啟用]。 您所選取的計劃可以與虛擬網路位於相同或不同的訂用帳戶中，但兩個訂用帳戶必須都與同一個 Azure Active Directory 租用戶關聯。|

已為虛擬網路啟用「標準 DDoS」時，您無法將虛擬網路移到另一個資源群組或訂用帳戶。 如果您需要移動已啟用「標準 DDoS」的虛擬網路，請先將「標準 DDoS」停用，移動虛擬網路，然後再啟用「標準 DDoS」。 移動之後，就會重設虛擬網路中所有受保護公用 IP 位址的自動調整原則閾值。

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>針對現有的虛擬網路啟用 DDoS 保護

1. 如果您沒有現有的 DDoS 保護計劃，請完成[建立 DDoS 保護計劃](#create-a-ddos-protection-plan)中的步驟來建立 DDoS 保護計劃。
2. 選取 Azure 入口網站左上角的 [ **建立資源** ]。
3. 在入口網站頂端的 [搜尋資源、服務及文件] 方塊中，輸入您要為其啟用「標準 DDoS 保護」的虛擬網路名稱。 當虛擬網路的名稱出現在搜尋結果中時，請選取它。
4. 選取 [設定] 底下的 [DDoS 保護]。
5. 選取 [標準]。 在 [DDoS 保護計劃] 底下，選取現有的 DDoS 保護計劃或是您在步驟 1 中建立的計劃，然後選取 [儲存]。 您所選取的計劃可以與虛擬網路位於相同或不同的訂用帳戶中，但兩個訂用帳戶必須都與同一個 Azure Active Directory 租用戶關聯。

## <a name="validate-and-test"></a>驗證和測試

首先，請查看 DDoS 保護計劃的詳細資料：

1. 選取入口網站左上角的 [所有服務]。
2. 在 [篩選] 方塊中，輸入 *DDoS* 。 當 **DDoS 保護計劃** 出現在結果中時，請選取它。
3. 從清單中選取您的 DDoS 保護計劃。

應該會列出 _MyVnet_ 的虛擬網路。 

## <a name="clean-up-resources"></a>清除資源

您可以保留資源以供下一個教學課程之用。 如果不再需要，請刪除 _MyResourceGroup_ 資源群組。 當您刪除資源群組時，也會刪除 DDoS 保護計劃及其所有相關資源。 如果您不打算使用此 DDoS 保護計劃，您應該移除資源，以避免不必要的費用。

   >[!WARNING]
   >此動作無法復原。

1. 在 Azure 入口網站中，搜尋並選取 **資源群組** ，或從 Azure 入口網站功能表選取 **資源群組** 。

2. 篩選或向下滾動以尋找 _MyResourceGroup_ 資源群組。

3. 選取資源群組，然後選取 [刪除資源群組]。

4. 輸入資源群組名稱以確認，然後選取 [刪除]。

停用虛擬網路的 DDoS 保護： 

1. 在入口網站頂端的 [搜尋資源、服務及文件] 方塊中，輸入您要停用其「標準 DDoS 保護」的虛擬網路名稱。 當虛擬網路的名稱出現在搜尋結果中時，請選取它。
2. 選取 [ **DDoS Protection Standard] 下** 的 [ **停** 用]。

如果您想要刪除 DDoS 保護計劃，您必須先中斷所有虛擬網路與其的關聯。 

## <a name="next-steps"></a>下一步

若要瞭解如何為您的 DDoS 保護計劃查看及設定遙測，請繼續進行教學課程。

> [!div class="nextstepaction"]
> [檢視和設定 DDoS 保護遙測](telemetry-monitoring-alerting.md)
