---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553236"
---
請依照下列步驟來連線到儲存體帳戶及驗證連線。

1. 在「儲存體總管」中，開啟 [連線至 Azure 儲存體]  對話方塊。 在 [連線至 Azure 儲存體]  對話方塊中，選取 [使用儲存體帳戶名稱和金鑰]  。

    ![螢幕擷取畫面顯示 [連線到 Azure 儲存體] 對話方塊，其中已選取 [使用儲存體帳戶名稱和金鑰]。](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. 貼上您的 [帳戶名稱]  和 [帳戶金鑰]  (來自本機 Web UI 中 [連線並複製]  頁面的金鑰 1 值)。 選取 [其他 (在下方輸入)]  作為儲存體端點網域，然後提供 Blob 服務端點，如下所示。 請只在透過 *http* 傳輸的情況下，才勾選 [使用 HTTP]選項。 如果使用 *https*，請將此選項維持不勾選。 選取 [下一步]  。

    ![螢幕擷取畫面顯示 [使用名稱和金鑰連線] 對話方塊及輸入的值。](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. 在 [連線摘要]  對話方塊中，檢閱所提供的資訊。 選取 [連接]  。

    ![螢幕擷取畫面顯示 [連線摘要] 對話方塊，並已選取 [連線]。](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. 您成功新增的帳戶會顯示在「儲存體總管」的左側窗格中，其名稱會附加「(外部，其他)」。 請按一下 [Blob 容器]  來檢視該容器。

    ![螢幕擷取畫面顯示已選取 [Blob 容器] 的 [總管] 功能表。](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
