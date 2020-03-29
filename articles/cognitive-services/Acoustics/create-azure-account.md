---
title: 專案聲學 Azure 批次處理帳戶設置
titlesuffix: Azure Cognitive Services
description: 此方法介紹設置 Azure 批次處理帳戶以用於專案聲學統一和虛幻引擎集成。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855085"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>專案聲學 Azure 批次處理帳戶設置
此方法介紹設置 Azure 批次處理帳戶以用於專案聲學統一和虛幻引擎集成。

## <a name="get-an-azure-subscription"></a>取得 Azure 訂用帳戶
您必須先有 [Azure 訂用帳戶](https://azure.microsoft.com/free/)，才能設定 Batch 與「儲存體」帳戶。 如果您是第一次註冊，Azure 會提供一些有時間限制的免費資源與 200 美元的信用額度。

## <a name="create-azure-batch-and-storage-accounts"></a>建立 Azure Batch 與儲存體帳戶
接著，請依照[這些指示](https://docs.microsoft.com/azure/batch/batch-account-create-portal)來設定 Azure Batch 和相關的「Azure 儲存體」帳戶。

為 Batch 與「儲存體」帳戶選取預設選項：
  
  ![顯示預設設置的 Azure 批次處理新帳戶選項的螢幕截圖](media/new-batch-account-create.png)

  ![顯示預設設置的 Azure 存儲新帳戶選項的螢幕截圖](media/batch-storage-account-create.png)

Azure 需要幾分鐘的時間來部署帳戶。 在入口網站的右上角即可找到完整的通知。
  
  ![Azure 帳戶已部署通知的螢幕截圖](media/batch-accounts-deploy-notification.png)

您現在應該可以在儀表板上看到您的帳戶。
  
  ![顯示批次處理和存儲帳戶的 Azure 門戶儀表板的螢幕截圖](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>使用 Azure 認證來設定聲場製作 UI
按一下儀表板上的 Batch 帳戶連結，然後按一下 Batch 帳戶頁面上的 [金鑰]**** 連結以存取您的認證。
  
  ![突出顯示連結到"金鑰"頁的 Azure 批次處理帳戶的螢幕截圖](media/batch-access-keys.png)

  ![具有訪問金鑰的 Azure 批次處理帳戶金鑰頁的螢幕截圖](media/batch-keys-info.png)

按一下頁面上的 [儲存體帳戶]**** 連結，以存取您的「Azure 儲存體」帳戶認證。
  
  ![包含訪問金鑰的 Azure 存儲帳戶金鑰頁面的螢幕截圖](media/storage-keys-info.png)

在[Unity 烘焙外掛程式](unity-baking.md)或[虛幻烘焙外掛程式](unreal-baking.md)中輸入這些憑據。

## <a name="node-types-and-region-support"></a>節點類型與區域支援
專案聲學需要 Fsv2 和 H 系列計算優化的 Azure VM 節點，這些節點可能並非在所有 Azure 區域中都受支援。 請參閱[此表格](https://azure.microsoft.com/global-infrastructure/services)，以確保您為 Batch 帳戶選取的位置正確。
![按區域顯示 Azure 虛擬機器的螢幕截圖](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>升級您的配額
建立 Azure Batch 帳戶時，為帳戶佈建的計算核心數目限制為 20 個。 我們可能希望提高此限制以加快烘焙時間，因為您可以在多個節點上並行化聲學工作負載，最多到場景中的探測點數。 您可以按一下 Azure Batch 入口網站頁面上的 [配額]**** 連結，然後按一下 [申請加大配額]****，來申請加大配額：

![Azure 配額頁面的螢幕截圖](media/azure-quotas.png)

## <a name="next-steps"></a>後續步驟
* 將專案聲學外掛程式集成到[您的 Unity](unity-integration.md)或[虛幻](unreal-integration.md)專案中

