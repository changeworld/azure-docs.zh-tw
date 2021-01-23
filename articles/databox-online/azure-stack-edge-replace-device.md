---
title: 更換您的 Azure Stack Edge Pro 裝置 |Microsoft Docs
description: 說明如何取得更換 Azure Stack Edge Pro 裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 91cb446da31f353162f90778d855056a9697d455
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726562"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>更換您的 Azure Stack Edge Pro 裝置

本文說明如何取代 Azure Stack Edge Pro 裝置。 當現有裝置發生硬體故障或需要升級時，就需要替換裝置。 


在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 開啟硬體問題的支援票證
> * 在 Azure 入口網站中建立替代裝置的新訂單
> * 安裝、啟動更換裝置
> * 傳回原始裝置

## <a name="open-a-support-ticket"></a>開啟支援票證

如果您現有的裝置發生硬體失敗，請遵循下列步驟來開啟支援票證：

1. 向 Microsoft 支援服務開啟支援票證，表示您想要退回裝置。 選取 [ **Azure Stack Edge Pro 硬體** 問題類型]，然後選擇 [ **硬體問題** ] 子類型。  

    ![開啟支援票證](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft 支援服務工程師會與您聯繫，以判斷欄位更換單位 (FRU) 是否可以修正問題，並可供此實例使用。 如果無法使用 FRU 或裝置需要硬體升級，則支援人員會引導您放置新的訂單，並傳回舊的裝置。

## <a name="create-a-new-order"></a>建立新訂單

遵循 [建立新資源](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)中的步驟，以建立新的資源來啟用您的更換裝置。

> [!NOTE]
> 不支援針對現有的資源啟用替代裝置。 新的資源會被視為新的訂單。 您將在裝置寄送到您的14天前開始計費。

## <a name="install-and-activate-the-replacement-device"></a>安裝並啟用更換裝置

請遵循下列步驟來安裝和啟動更換裝置：

1. [安裝您的裝置](azure-stack-edge-deploy-install.md)。
2. 針對您稍早建立的新資源[啟用您的裝置](azure-stack-edge-deploy-connect-setup-activate.md)。

## <a name="return-your-existing-device"></a>返回您現有的裝置

依照所有步驟來退回原始裝置：

1. [清除裝置上的資料](azure-stack-edge-return-device.md#erase-data-from-the-device)。
2. 起始原始裝置的[裝置回](azure-stack-edge-return-device.md#initiate-device-return)撥。
3. [安排取貨](azure-stack-edge-return-device.md#schedule-a-pickup)。
4. 當 Microsoft 收到裝置之後，您就可以刪除與所傳回裝置相關聯 [的資源](azure-stack-edge-return-device.md#delete-the-resource) 。


## <a name="next-steps"></a>後續步驟

- 瞭解如何 [退回 Azure Stack Edge Pro 裝置](azure-stack-edge-return-device.md)。
