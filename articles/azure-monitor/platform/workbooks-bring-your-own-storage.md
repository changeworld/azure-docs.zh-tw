---
title: Azure 監視器活頁簿攜帶您自己的儲存體
description: 瞭解如何將活頁簿內容儲存到您的儲存體，以保護您的活頁簿
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 09ea29d8149298f906672979ea76da0558a2934c
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371129"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>攜帶您自己的儲存體以儲存活頁簿

有時候您可能會有想要保護的查詢或某些商務邏輯。 活頁簿會將活頁簿內容儲存到您的儲存體，以提供保護活頁簿的選項。 然後，您可以使用 Microsoft 管理的金鑰來加密儲存體帳戶，也可以藉由提供您自己的金鑰來管理加密。 [請參閱儲存體服務加密上的 Azure 檔。](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>使用受控識別儲存活頁簿

1. 將活頁簿儲存到儲存體之前，您必須先建立受控識別 (所有服務 > 受控識別) 並讓它 `Storage Blob Data Contributor` 存取您的儲存體帳戶。 [請參閱受控識別的 Azure 檔。](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![顯示新增角色指派的螢幕擷取畫面](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. 建立新的活頁簿。
3. 選取 [ **儲存** ] 按鈕來儲存活頁簿。
4. 您可以 `Save content to an Azure Storage Account` 選擇選取核取方塊來儲存至 Azure 儲存體帳戶。

    ![顯示已儲存之對話方塊的螢幕擷取畫面](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. 選取想要的儲存體帳戶和容器。 儲存體帳戶清單來自上面選取的訂用帳戶。

    ![顯示 [儲存] 對話方塊與儲存體選項的螢幕擷取畫面](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. 然後選取 [ **變更** ] 以選取先前建立的受控識別。

    [![顯示 [變更身分識別] 對話方塊的螢幕擷取畫面](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. 選取您的儲存選項之後，請按 [ **儲存** ] 以儲存您的活頁簿。

## <a name="limitations"></a>限制

- 儲存至自訂儲存體時，您無法將活頁簿的個別部分釘選到儀表板，因為個別的 pin 會在儀表板本身包含受保護的資訊。 使用自訂儲存體時，您只能將活頁簿本身的連結釘選到儀表板。
- 活頁簿儲存至自訂儲存體之後，一律會儲存至自訂儲存體，而且無法關閉。 若要儲存在其他位置，您可以使用 [另存新檔]，並選擇不要將複本儲存至自訂儲存體。
- Application Insights 資源中的活頁簿是「舊版」活頁簿，且不支援自訂儲存體。 Application Insights 資源中最新的活頁簿是「.。。更多] 選取專案。 舊版活頁簿在儲存時沒有訂閱選項。

   ![顯示舊版活頁簿的螢幕擷取畫面](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何在活頁簿中建立 [地圖](workbooks-map-visualizations.md) 視覺效果。
- 瞭解如何在活頁 [簿中使用群組](workbooks-groups.md)。