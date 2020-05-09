---
title: 搭配 Azure 自動化使用動態群組更新管理
description: 本文說明動態群組如何與 Azure 自動化更新管理搭配使用。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: bf4c156d2bf9c205bd7545a96b5314dd43b2d02c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690782"
---
# <a name="use-dynamic-groups-with-update-management"></a>搭配更新管理使用動態群組

更新管理可讓您將 Azure 或非 Azure Vm 的動態群組設為目標，以進行更新部署。 使用動態群組可讓您不必編輯部署來更新機器。

> [!NOTE]
> 動態群組無法與傳統 Vm 搭配使用。

您可以從 Azure 入口網站中的 [**更新管理**] 定義 azure 或非 azure 機器的動態群組。 請參閱[管理多個 Azure 虛擬機器的更新](manage-update-multi.md)。

動態群組是由 Azure 自動化在部署階段評估的查詢所定義。 即使動態群組查詢會抓取大量的機器，Azure 自動化一次只能處理最多1000部機器。 請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management)。 

> [!NOTE]
> 如果您預期會更新1000部以上的電腦，建議您在多個更新排程之間分割更新。 

## <a name="define-dynamic-groups-for-azure-machines"></a>定義 Azure 機器的動態群組

為 Azure 機器定義動態群組查詢時，您可以使用下列專案來填入動態群組：

* 訂用帳戶
* 資源群組
* 位置
* Tags

![選取群組](./media/automation-update-management/select-groups.png)

若要預覽動態群組查詢的結果，請按一下 [**預覽**]。 預覽會顯示目前時間的群組成員資格。 在此範例中，我們會搜尋具有群組**BackendServer**標記`Role`的機器。 如果有更多電腦新增此標籤，則會將其新增至該群組的任何未來部署。

![預覽群組](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>定義非 Azure 機器的動態群組

非 Azure 機器的動態群組會使用已儲存的搜尋，也稱為「電腦群組」。 若要瞭解如何建立已儲存的搜尋，請參閱[建立電腦群組](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 建立儲存的搜尋之後，您可以在 Azure 入口網站的 [**更新管理**] 中，從已儲存的搜尋清單中加以選取。 按一下 [**預覽**] 以預覽已儲存搜尋中的電腦。

![選取群組](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>後續步驟

建立動態群組之後，您可以[建立更新部署](automation-tutorial-update-management.md)。
