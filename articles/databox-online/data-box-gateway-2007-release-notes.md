---
title: Azure Stack Edge & Azure 資料箱閘道2007版本資訊 |Microsoft Docs
description: 描述執行2007版本之 Azure Stack Edge 和資料箱閘道的重大未解決問題和解決方法。
services: databox
author: priestlg
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 07/14/2020
ms.author: v-grpr
ms.openlocfilehash: 4964454a5636120840fbdb5f3fbdd3aee30d8e74
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531317"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Azure Stack Edge 和 Azure 資料箱 Gateway 2007 版本資訊

下列版本資訊會針對 Azure Stack Edge 和資料箱閘道的2007版本，識別重大的未解決問題和已解決的問題。

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 在您部署 Azure Stack Edge/資料箱閘道之前，請仔細檢查版本資訊中包含的資訊。

此版本對應于軟體版本：

- **Azure Stack Edge 2007 （1.6.1280.1667）** -KB 4566549
- **資料箱閘道2007（1.6.1280.1667）** -KB 4566550

> [!NOTE]
> 更新2007只能套用至所有執行軟體或更新版本之公開上市（GA）版本的裝置。

## <a name="whats-new"></a>新功能

此版本包含下列 Bug 修正：

- **上傳問題**-此版本修正由於失敗而上傳重新開機的上傳問題，可能會使上傳完成的速率變慢。 上傳主要的資料集時，可能會發生此問題，這是因為頻寬節流作用時，其大小與可用頻寬（尤其是但不限於）中的檔案很大。 這種變更可確保在重新開機指定檔案的上傳之前，有足夠的機會可供上傳完成。

此版本也包含下列更新：

- Windows VHD 的基底映射已更新。
- 所有累積的 Windows 更新和 .NET framework 更新都包含在2020年5月發行。
- 此版本支援 Azure Stack Edge 裝置上的 IoT Edge 1.0.9.3。

## <a name="known-issues-in-this-release"></a>此版本已知的問題

此版本未注明任何新問題。 所有已注明的發行問題都已從先前的版本中執行。 若要查看已知問題的清單，請移至[GA 版本中的已知問題](data-box-gateway-release-notes.md#known-issues-in-ga-release)。

## <a name="next-steps"></a>後續步驟

- [準備部署 Azure Stack Edge](data-box-edge-deploy-prep.md)
- [準備部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
