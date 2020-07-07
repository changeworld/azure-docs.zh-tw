---
title: Azure 資料箱閘道 & Azure Data Box Edge 1906 版本資訊 |Microsoft Docs
description: 說明 Azure 資料箱閘道的重大未解決問題和解決方法，以及執行1906版本的 Azure Data Box Edge。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 9f4633a3fcafcb2b3151270715036e7940eb1c91
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82561838"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge 和 Azure 資料箱閘道1906版本資訊

下列版本資訊會針對 Azure Data Box Edge 和 Azure 資料箱閘道的1906版本，識別重大的未解決問題和已解決的問題。 

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 在您部署 Data Box Edge/Data Box Gateway 之前，請仔細檢查版本資訊中包含的資訊。

此版本對應于軟體版本：

- **資料箱閘道1906（1.6.978.743）**
- **Data Box Edge 1906 （1.6.978.743）**

> [!NOTE]
> 更新1906只能套用至執行正式運作（GA）或1905版軟體的 Data Box Edge 裝置。

## <a name="whats-new"></a>最新消息

- **修正修復金鑰管理工作流程中的錯誤**-在舊版中，有一個 bug 原因，未套用修復金鑰。 此版本已經修正這個錯誤。 我們強烈建議您套用此更新，因為修復金鑰可讓您在裝置無法開機的事件中復原裝置上的資料。 如需詳細資訊，請參閱[在部署 Data Box Edge 或資料箱閘道時，如何儲存修復金鑰](azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)。
- 現場可程式化網**關陣列（FPGA）記錄改進**-開始進行1905版本、記錄和與 FPGA 相關的警示增強功能。 如果您使用 Edge 計算功能搭配 FPGA，這會繼續成為 Data Box Edge 的必要更新。 如需詳細資訊，請參閱如何[在您的 Data Box Edge 上使用 Edge 計算來轉換資料](azure-stack-edge-deploy-configure-compute-advanced.md)。

## <a name="known-issues-in-ga-release"></a>GA 版本中的已知問題

此版本未注明任何新問題。 所有已注明的發行問題都已從先前的版本中執行。 若要查看已知問題的清單，請移至[GA 版本中的已知問題](data-box-gateway-release-notes.md#known-issues-in-ga-release)。


## <a name="next-steps"></a>後續步驟

- [準備部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [準備部署 Azure Data Box Edge](azure-stack-edge-deploy-prep.md)
