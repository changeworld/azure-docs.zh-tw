---
title: Azure DevTest Labs 中的活動記錄 |Microsoft Docs
description: 本文提供的步驟可讓您查看 Azure DevTest Labs 的活動記錄。
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 24b5eb25f1c8c9044ad6e77545381a43a11714f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096169"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中查看實驗室的活動記錄 
在您建立一或多個實驗室之後，您可能會想要監視您的實驗室存取、修改和管理的方式和時機，以及人員。 Azure DevTest Labs 使用 Azure 監視器（特別是 **活動記錄**）來為實驗室提供這些作業的相關資訊。 

本文說明如何在 Azure DevTest Labs 中查看實驗室的活動記錄。

## <a name="view-activity-log-for-a-lab"></a>查看實驗室的活動記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [所有服務]****，然後選取 [DEVOPS]**** 區段中的 [DevTest Labs]****。 如果您選取 [DEVOPS]**** 區段中 [DevTest Labs]**** 旁邊的 * (星號)， 此動作會將 [DevTest Labs]**** 新增到左側導覽功能表，以便您下次輕鬆存取。 然後，您便可以選取左側導覽功能表上的 [DevTest Labs]****。

    ![所有服務 - 選取 DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. 從實驗室清單中選取您的實驗室。
1. 在實驗室的首頁上，選取左側功能表上的 [設定 **與原則** ]。 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="選取左側功能表上的 [設定與原則]":::
1. 在 [設定**與原則**] 頁面上，選取左側功能表中 [**管理**] 底下的 [**活動記錄**]。 您應該會看到在實驗室中完成之作業的專案。 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="選取左側功能表上的 [設定與原則]":::    
1. 選取事件以查看其詳細資料。 在 [ **摘要** ] 頁面上，您會看到作業名稱、時間戳記以及作業的人員等資訊。 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="選取左側功能表上的 [設定與原則]":::        
1. 切換至 [ **JSON** ] 索引標籤以查看更多詳細資料。 在下列範例中，您可以看到 VM 的名稱，以及在 VM 上完成的作業 (已停止) 。

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="選取左側功能表上的 [設定與原則]":::           
1. 切換至 [ **變更歷程記錄 (預覽]) ** 索引標籤，以查看變更的歷程記錄。 在下列範例中，您會看到在 VM 上進行的變更。 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="選取左側功能表上的 [設定與原則]":::             
1. 選取變更歷程記錄清單中的變更，以查看更多關於變更的詳細資料。 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="選取左側功能表上的 [設定與原則]":::             

如需有關活動記錄的詳細資訊，請參閱 [Azure 活動記錄](../azure-monitor/platform/activity-log.md)。

## <a name="next-steps"></a>接下來的步驟

- 若要瞭解如何設定活動記錄 **警示** ，請參閱 [建立警示](create-alerts.md)。
- 若要深入瞭解活動記錄，請參閱  [Azure 活動記錄](../azure-monitor/platform/activity-log.md)。

