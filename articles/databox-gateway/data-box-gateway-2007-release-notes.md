---
title: Azure Stack Edge & Azure 資料箱閘道2007版本資訊 |Microsoft Docs
description: 描述執行2007版本的 Azure Stack Edge 和資料箱閘道的重大開啟問題和解決方式。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581810"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Azure Stack Edge 和 Azure 資料箱閘道2007版本資訊

下列版本資訊指出 Azure Stack Edge 和資料箱閘道2007版的重大未解決問題和解決的問題。

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 部署 Azure Stack Edge/資料箱閘道之前，請仔細檢查版本資訊中所含的資訊。

此版本對應于軟體版本：

- **Azure Stack Edge 2007 (1.6.1280.1667)** -KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** -KB 4566550

> [!NOTE]
> 更新2007只能套用至正式運作 (正式運作) 軟體版本或更新版本的所有裝置。

## <a name="whats-new"></a>最新消息

此版本包含下列 Bug 修正：

- **上傳問題** -此版本修正了上傳問題，因為失敗而上傳重新開機可能會降低上傳完成的速率。 當上傳的資料集主要包含大小相當大的檔案（相對於可用頻寬的大小很大，尤其是在頻寬節流設定為使用中時），就會發生此問題。 這項變更可確保在重新開機指定檔案的上傳之前，上傳完成有足夠的機會。

此版本也包含下列更新：

- Windows VHD 的基底映射已更新。
- 所有累積的 Windows 更新和 .NET framework 更新都包含在2020年5月發行。
- 此版本支援 Azure Stack Edge 裝置上的 IoT Edge 1.0.9.3。

## <a name="known-issues-in-this-release"></a>此版本已知的問題

未在此版本中注明任何新問題。 所有發行的問題都已從先前的版本執行。 若要查看已知問題的清單，請移至 [GA 版本中的已知問題](data-box-gateway-release-notes.md#known-issues-in-ga-release)。

## <a name="next-steps"></a>後續步驟

- [準備部署 Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)
- [準備部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
