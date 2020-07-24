---
title: 取代您的 Azure Stack Edge 裝置 |Microsoft Docs
description: 說明如何取得 Azure Stack Edge 裝置的更換。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 5d5e8cc3a959c7800019107d2c682e144449b46c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096409"
---
# <a name="replace-your-azure-stack-edge-device"></a>取代您的 Azure Stack Edge 裝置

本文說明如何取得 Azure Stack Edge 裝置的更換。 當現有裝置發生硬體故障或需要升級時，就需要替換裝置。 


在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 開啟硬體問題的支援票證
> * 在 Azure 入口網站中建立替代裝置的新資源
> * 安裝、啟用更換裝置
> * 傳回原始裝置

## <a name="open-a-support-ticket"></a>開啟支援票證

如果您現有的裝置發生硬體故障，請開啟支援票證。 Microsoft 支援服務將判斷現場替換單元 (FRU) 不適用於此執行個體，或裝置需要硬體升級。 不論是哪種情況，支援服務都會訂購替換裝置。

1. 向 Microsoft 支援服務開啟支援票證，表示您想要退回裝置。 針對問題類型選取 [Azure Stack Edge 硬體]。

    ![開啟支援票證](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft 支援服務將會與您連絡。 提供運送詳細資料。
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>建立替代裝置的資源

請遵循下列步驟來建立資源。

1. 請遵循[建立新資源](azure-stack-edge-deploy-prep.md#create-a-new-resource)中的步驟，建立替代裝置的資源。 

2. 請務必針對 [我有 Azure Stack Edge 裝置]，選取此核取方塊。 

    ![取代裝置的資源](media/azure-stack-edge-replace-device/replace-resource-1.png)  

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

- 瞭解如何傳回[Azure Stack Edge 裝置](azure-stack-edge-return-device.md)。
