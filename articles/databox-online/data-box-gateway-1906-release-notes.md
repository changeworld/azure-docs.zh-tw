---
title: Azure 資料箱閘道 & Azure 資料箱 Edge 1906 版本資訊 |Microsoft Docs
description: 描述執行1906版的 Azure 資料箱閘道和 Azure 資料箱 Edge 的重大開啟問題和解決方式。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 9f4633a3fcafcb2b3151270715036e7940eb1c91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82561838"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure 資料箱 Edge 和 Azure 資料箱閘道1906版本資訊

下列版本資訊指出 Azure 資料箱 Edge 和 Azure 資料箱閘道的1906版的重大未解決問題和解決的問題。 

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 在您部署 Data Box Edge/Data Box Gateway 之前，請仔細檢查版本資訊中所包含的資訊。

此版本對應于軟體版本：

- **Data Box Gateway 1906 (1.6.978.743) **
- **Data Box Edge 1906 (1.6.978.743) **

> [!NOTE]
> 更新1906只能套用至 (GA) 或1905版軟體正式運作的 Data Box Edge 裝置。

## <a name="whats-new"></a>新功能

- **修正修復金鑰管理工作流程中的 bug** -在舊版中，發生未套用修復金鑰的錯誤原因。 此版本已經修正這個錯誤。 強烈建議您套用此更新，因為修復金鑰可讓您在裝置未開機的事件中復原裝置上的資料。 如需詳細資訊，請參閱如何 [在部署 Data Box Edge 或 Data Box Gateway 時儲存修復金鑰](azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)。
- 可現場程式化網**關陣列 (FPGA) 記錄改進**-開始1905版本、記錄以及與 FPGA 相關的警示增強功能。 如果您搭配使用 Edge 計算功能與 FPGA，這會持續成為 Data Box Edge 的必要更新。 如需詳細資訊，請參閱如何 [在 Data Box Edge 上使用 Edge 計算來轉換資料](azure-stack-edge-deploy-configure-compute-advanced.md)。

## <a name="known-issues-in-ga-release"></a>GA 版本的已知問題

未在此版本中注明任何新問題。 所有發行的問題都已從先前的版本執行。 若要查看已知問題的清單，請移至 [GA 版本中的已知問題](data-box-gateway-release-notes.md#known-issues-in-ga-release)。


## <a name="next-steps"></a>接下來的步驟

- [準備部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [準備部署 Azure Data Box Edge](azure-stack-edge-deploy-prep.md)
