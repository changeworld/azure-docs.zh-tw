---
title: 建立受控私人端點以連接到您的資料來源結果
description: 本文將告訴您如何從 Azure Synapse 工作區，為您的資料來源建立受控私人端點。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: fae958542f1bc391f285104d80d1e19131470abe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065483"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>為您的資料來源建立受控私用端點（預覽）

本文將告訴您如何在 Azure 中為您的資料來源建立受控私人端點。 若要深入瞭解，請參閱[受控私用端點](./synapse-workspace-managed-private-endpoints.md)。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>步驟1：在 Azure 入口網站中開啟您的 Azure Synapse 工作區

您可以從 Azure Synapse Studio 為您的資料來源建立受控私用端點。 選取 Azure 入口網站中的 [**總覽**] 索引標籤，然後選取 [**啟動 Synapse Studio**]。
![啟動 Azure Synapse Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>步驟2：流覽至 Synapse Studio 中的 [受管理的虛擬網路] 索引標籤

在 Azure Synapse Studio 中，從左側導覽中選取 [**管理**] 索引標籤。 選取 [**受控虛擬網路**]，然後選取 [ **+ 新增**]。
![建立新的受控私人端點](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>步驟3：選取資料來源類型

選取 [資料來源類型]。 在此情況下，目標資料來源是 ADLS Gen2 帳戶。 選取 \[繼續\]。
![選取目標資料來源類型](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>步驟4：輸入資料來源的相關資訊

在下一個視窗中，輸入資料來源的相關資訊。 在此範例中，我們要建立 ADLS Gen2 帳戶的受控私人端點。 輸入受控私人端點的 [**名稱**]。 提供**Azure 訂**用帳戶和**儲存體帳戶名稱**。 選取 [建立]。
![輸入目標資料來源詳細資料](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>步驟5：確認已成功建立您的受控私人端點

提交要求之後，您會看到其狀態。 若要確認已建立成功建立受控私用端點，請檢查其布建*狀態*。 您可能需要等候 1**分鐘，然後選取 [** 重新整理] 來更新布建狀態。 您可以看到已成功建立 ADLS Gen2 帳戶的受控私人端點。

您也可以看到*核准狀態*為 [*擱置*]。 目標資源的擁有者可以核准或拒絕私人端點連接要求。 如果擁有者核准私用端點連接要求，則會建立私人連結。 若拒絕，則不會建立私用連結。
![受控私人端點建立要求狀態](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>接下來的步驟

深入了解[受控私人端點](./synapse-workspace-managed-private-endpoints.md)