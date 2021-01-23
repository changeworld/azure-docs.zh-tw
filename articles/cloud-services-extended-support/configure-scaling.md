---
title: 設定 Azure 雲端服務 (延伸支援) 的調整
description: 如何啟用 Azure 雲端服務 (延伸支援) 的調整選項
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 482c11395ff5dbbf2dd5bb8100451c3442f2f333
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744331"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>使用 Azure 雲端服務 (延伸支援設定調整選項)  

您可以設定條件來啟用雲端服務 (延伸支援) 部署，以相應縮小和放大。這些情況可能會以 CPU 使用量、磁片負載和網路負載為基礎。 

設定雲端服務部署的調整時，請考慮下列資訊：
- 調整會影響核心使用量。 較大的角色實例耗用更多核心，您只能在訂用帳戶的核心限制內調整規模。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。
- 支援根據佇列訊息閾值進行調整。 如需詳細資訊，請參閱 [開始使用 Azure 佇列儲存體](https://docs.microsoft.com/azure/storage/queues/storage-dotnet-how-to-use-queues)。
- 若要確保雲端服務的高可用性 () 應用程式的延伸支援，請務必部署兩個以上的角色實例。
- 只有當所有角色都處於 **就緒** 狀態時，才會發生自訂自動調整。

## <a name="configure-and-manage-scaling"></a>設定和管理調整

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 選取您要設定調整規模的雲端服務 (擴充支援) 部署。 
3. 選取 [ **調整** ] 分頁。 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="影像顯示在 Azure 入口網站中選取 [遠端桌面] 選項":::

4. 頁面將會顯示可設定調整的所有角色清單。 選取您要設定的角色。 
5. 選取您要設定的縮放類型
    - **手動調整** 會設定實例的絕對計數。
        1. 選取 [ **手動調整**]。
        2. 輸入您想要擴大或縮小的實例數目。
        3. 選取 [儲存]。

        :::image type="content" source="media/enable-scaling-2.png" alt-text="影像顯示在 Azure 入口網站中設定手動調整":::

        4. 調整作業將會立即開始。 
        
    - **自訂自動** 調整可讓您設定規則，以控制要調整多少或多小。 
        1. 選取 **自訂自動** 調整
        2. 選擇根據度量或實例計數進行調整。

        :::image type="content" source="media/enable-scaling-3.png" alt-text="影像顯示在 Azure 入口網站中設定自訂自動調整":::

        3. 如果根據計量進行調整，請視需要新增規則，以達成所需的調整結果。

        :::image type="content" source="media/enable-scaling-4.png" alt-text="影像顯示在 Azure 入口網站中設定自訂自動調整規則":::

        4. 選取 [儲存]。
        5. 當規則引發時，就會開始調整規模作業。
        
6. 您可以藉由選取 [調整] 索引卷 **標** ，來查看或調整套用至您部署的現有調整規則。

    :::image type="content" source="media/enable-scaling-5.png" alt-text="影像顯示調整 Azure 入口網站中現有的調整規則":::

## <a name="next-steps"></a>後續步驟 
- 請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 。
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。
