---
title: Azure IoT Edge 模組供應專案發佈總覽 |Azure Marketplace
description: 在 Microsoft Azure Marketplace 上發佈 IoT Edge 供應項目之流程概觀。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: e9116e5cdb3bd9ed61205ceabd4d51c96c6aadc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144659"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge 模組供應項目發佈概觀

>[!Important]
>從2020年4月13日開始，我們會開始將您的 IoT Edge 課程模組的管理工作移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請依照[建立 IoT Edge 模組供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)專案中的指示來管理您遷移的供應專案。

<table> <tr> <td>本節說明如何將新的 Azure IoT Edge 模組供應項目發佈至 Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>。 IoT Edge 模組是專門設計在 IoT Edge 裝置上執行的 Docker 相容容器。 Azure IoT Edge 模組是 IoT Edge 管理的最小計算單位，可以包含 Azure 服務或自訂的解決方案程式碼。 </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>發佈程序

發佈 IoT Edge 模組供應項目主要步驟如下：

1. 建立供應項目<br> 提出供應項目詳細資訊。 這些資訊包括：供應項目說明、行銷資料、支援資訊，以及資產規格。

2. 建立商業及技術資產<br> 建立商業資產 (法律文件和行銷資料) 和相關聯解決方案之技術資產 (裝載於 Azure Container Registry 之 IoT Edge 模組映像)。

3. 建立 SKU<br> 建立與供應項目相關聯的 SKU。 您需要針對預計發佈的每個映像提供個別的 SKU。

4. 認證並發佈供應項目 <br>完成供應項目和技術的資產之後，您就可以提交供應項目。 提交作業將啟動發佈程序。 在此程序中，解決方案會經過測試、驗證、認證，然後在 Microsoft Azure Marketplace「上線」。

## <a name="parts-of-an-offer"></a>供應項目細項

以下文章概括了 IoT Edge 模組供應項目的各主要部分細項。

- [先決條件](./cpp-prerequisites.md) <br>本文列出建立或發行的 IoT Edge 模組前需符合的技術和商業需求。
- [預備 IoT Edge 模組技術資產](./cpp-create-technical-assets.md) <br>本文說明如何準備 IoT Edge 模組之技術資產。 這些資產必須符合所有技術準則，才能將 IoT Edge 模組發佈至 Microsoft Azure Marketplace。
- [建立 IoT Edge 模組供應項目](./cpp-create-offer.md) <br>本文列出使用 [Cloud Partner 入口網站](https://cloudpartner.azure.com) 建立新 IoT Edge 供應項目所需步驟。
- [發佈 IoT Edge 模組供應項目](./cpp-publish-offer.md)<br> 本文說明如何將供應項目提交至 Microsoft Azure Marketplace 並發佈。

## <a name="next-steps"></a>後續步驟

檢閱發佈 IoT Edge 模組至 Microsoft Azure Marketplace 時需滿足的[技術和商務需求](./cpp-prerequisites.md)。
