---
title: '在雲端服務中啟用監視 (使用 Azure 入口網站的延伸支援) '
description: 使用 Azure 入口網站來啟用雲端服務的監視 (擴充支援) 實例
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744371"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>使用 Azure 入口網站來啟用雲端服務的監視 (延伸支援) 

本文說明如何在現有的雲端服務上啟用警示 (延伸支援) 部署。 

## <a name="add-monitoring-rules"></a>新增監視規則
1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 選取您要啟用警示的雲端服務 (延伸支援) 部署。 
3. 選取 [ **警示** ] 分頁。 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="影像會顯示選取 Azure 入口網站中的 [警示] 索引標籤。":::

4. 選取 **新的警示** 圖示。
     :::image type="content" source="media/enable-alerts-2.png" alt-text="顯示選取 [新增警示] 選項的影像。":::

5. 根據您感興趣追蹤的計量，輸入所需的條件和必要動作。 您可以根據個別計量或活動記錄來定義規則。 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="影像：顯示要將條件新增至警示的位置。":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="影像顯示設定信號邏輯。":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="影像顯示設定動作群組邏輯。":::

6. 當您完成警示的設定時，請儲存變更，然後根據設定的計量，您將會開始看到 [ **警示** ] 分頁在一段時間內填入。

## <a name="next-steps"></a>後續步驟 
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。