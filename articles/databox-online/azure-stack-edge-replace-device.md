---
title: 更換您的 Azure Stack Edge Pro 裝置 |Microsoft Docs
description: 說明如何取得更換 Azure Stack Edge Pro 裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: ec16a2b42b818e96399b8fdbad4a0951f84ef825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893892"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>更換您的 Azure Stack Edge Pro 裝置

本文說明如何取得更換 Azure Stack Edge Pro 裝置。 當現有裝置發生硬體故障或需要升級時，就需要替換裝置。 


在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 開啟硬體問題的支援票證
> * 在 Azure 入口網站中建立替代裝置的新資源
> * 安裝、啟動更換裝置
> * 傳回原始裝置

## <a name="open-a-support-ticket"></a>開啟支援票證

如果您現有的裝置有硬體故障，請開啟支援票證。 Microsoft 支援服務將判斷現場替換單元 (FRU) 不適用於此執行個體，或裝置需要硬體升級。 不論是哪種情況，支援服務都會訂購替換裝置。

1. 向 Microsoft 支援服務開啟支援票證，表示您想要退回裝置。 選取問題類型為 **Azure Stack Edge Pro 硬體**]。

    ![開啟支援票證](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft 支援服務將會與您連絡。 提供運送詳細資料。
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>建立替代裝置的資源

請遵循下列步驟來建立資源。

1. 請依照 [建立新資源](azure-stack-edge-deploy-prep.md#create-a-new-resource) 中的步驟來建立替代裝置的資源。 

2. 請務必選取 [ **我有 Azure Stack Edge Pro 裝置**] 的核取方塊。 

    ![更換裝置的資源](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>安裝並啟用更換裝置

請遵循下列步驟來安裝和啟動更換裝置：

1. [安裝您的裝置](azure-stack-edge-deploy-install.md)。

2. 針對您稍早建立的新資源[啟用您的裝置](azure-stack-edge-deploy-connect-setup-activate.md)。

## <a name="return-your-existing-device"></a>返回您現有的裝置

依照所有步驟來退回原始裝置：

1. [清除裝置上的資料](azure-stack-edge-return-device.md#erase-data-from-the-device)。
2. 起始原始裝置的[裝置回](azure-stack-edge-return-device.md#initiate-device-return)撥。
3. [安排取貨](azure-stack-edge-return-device.md#schedule-a-pickup)。
4. [刪除與所退回裝置相關聯的資源](azure-stack-edge-return-device.md#delete-the-resource)。


## <a name="next-steps"></a>後續步驟

- 瞭解如何 [退回 Azure Stack Edge Pro 裝置](azure-stack-edge-return-device.md)。
