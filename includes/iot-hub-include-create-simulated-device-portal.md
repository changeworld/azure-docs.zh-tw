---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67174601"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

接下來，建立裝置身分識別並儲存金鑰以供稍後使用。 模擬應用程式會使用此裝置身分識別來將訊息傳送到 IoT 中樞。 在 PowerShell 中或使用 Azure Resource Manager 範本時，無法使用這項功能。 下列步驟會指出如何使用 [Azure 入口網站](https://portal.azure.com)建立模擬裝置。

1. 開啟 [Azure 入口網站](https://portal.azure.com)並登入您的 Azure 帳戶。

2. 選取 [資源群組]  ，然後選擇您的資源群組。 本教學課程使用 **ContosoResources**。

3. 在資源清單中，選取您的 IoT 中樞。 本教學課程使用 **ContosoTestHub**。 在 [中樞] 窗格中選取 [IoT 裝置]  。

4. 選取 [+ 新增]  。 在 [新增裝置] 窗格中，填入裝置識別碼。 本教學課程使用 **Contoso-Test-Device**。 金鑰的部分保留空白，並勾選 [自動產生金鑰]  。 請確定 [將裝置連線到 IoT 中樞]  已啟用。 選取 [儲存]  。

   ![新增裝置畫面](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. 現在金鑰已建立，請選取裝置來查看產生的金鑰。 選取主要金鑰上的 [複製] 圖示，並將它儲存在「記事本」之類的位置中，以便在本教學課程的測試階段中使用。

   ![裝置詳細資料，包括金鑰](./media/iot-hub-include-create-simulated-device-portal/device-details.png)