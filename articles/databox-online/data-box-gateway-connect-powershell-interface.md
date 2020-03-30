---
title: 使用 Windows PowerShell 連接到和管理 Azure 資料盒閘道設備
description: 描述如何通過 Windows PowerShell 介面連接到資料框閘道並對其進行管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260211"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>通過 Windows PowerShell 管理 Azure 資料盒閘道設備

Azure 資料盒閘道解決方案允許您通過網路將資料發送到 Azure。 本文介紹了資料盒閘道設備的一些配置和管理工作。 您可以使用 Azure 門戶、本地 Web UI 或 Windows PowerShell 介面來管理設備。

本文重點介紹使用 PowerShell 介面執行的任務。

本文包括以下過程：

- 連接到 PowerShell 介面
- 建立支援封裝
- Upload certificate
- 在非 DHCP 環境中啟動
- 查看設備資訊

## <a name="connect-to-the-powershell-interface"></a>連接到 PowerShell 介面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>建立支援封裝

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>在非 DHCP 環境中啟動

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>查看設備資訊

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>後續步驟

- 在 Azure 入口網站中部署 [Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。
