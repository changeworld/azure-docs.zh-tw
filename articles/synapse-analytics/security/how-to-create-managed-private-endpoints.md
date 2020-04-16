---
title: 創建託管專用終結點以連接到數據源結果。
description: 本文將介紹如何從 Azure Synapse 工作區創建託管專用終結點到數據源。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428897"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>建立資料來源的託管專用終結點(預覽)

本文將介紹如何創建 Azure 中的數據源的託管專用終結點。 請參閱[託管專用終結點](./synapse-workspace-managed-private-endpoints.md)以瞭解更多資訊。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>第 1 步:在 Azure 門戶中打開 Azure 突觸工作區

可以從 Azure Synapse Studio 創建到資料源的託管專用終結點。 選擇 Azure 門戶中的 **「概述」** 選項卡,然後選擇**啟動同步工作室**。
![啟動 Azure 同步工作室](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>第二步:導覽到 Synapse 工作室中的託管虛擬網路選項卡

在 Azure 同步工作室中,從左側導航中選擇 **「管理**」選項卡。 選擇**託管虛擬網路**,然後選擇 **" 新建**"。
![建立新的託管專用終結點](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>第三步:選擇資料來源類型

選擇數據源類型。 在這種情況下,目標數據來源是 ADLS gen2 帳戶。 選取 [繼續]  。
![選擇目標資料來源](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>第四步:輸入有關資料來源的資訊

在下一個視窗中,輸入有關數據來源的資訊。 在此示例中,我們將創建 ADLS gen2 帳戶的託管專用終結點。 輸入託管專用終結點**的名稱**。 提供**Azure 訂閱**與**儲存帳戶名稱**。 選取 [建立]  。
![輸入目標資料來源詳細資訊](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>第 5 步:驗證託管專用終結點是否成功建立

提交請求后,您將看到其狀態。 要驗證成功建立託管專用終結點,請檢查其*預先狀態*。 您可能需要等待 1 分鐘,然後選擇 **「刷新」** 以更新預配狀態。 您可以看到 ADLS 第 2 代帳戶的託管專用終結點已成功創建。

您可以可以看到*批准狀態*復*原狀態*。 目標資源的所有者可以批准或拒絕專用終結點連接請求。 如果擁有者批准專用終結點連接請求,則建立專用連結。 如果被拒絕,則未建立專用連結。
![託管專用終結點建立要求狀態](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>後續步驟

瞭解有關[託管專用終結點](./synapse-workspace-managed-private-endpoints.md)的更多