---
title: 包含檔案
description: 包含檔案
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017465"
---
稍後在本教學課程中執行範例裝置應用程式時，您需要下列組態值：

* 識別碼範圍：在 IoT Central 應用程式中，瀏覽至 **管理 > 裝置連線**。 記下 **識別碼範圍** 值。
* 群組主要金鑰：在 IoT Central 應用程式中，瀏覽至 **管理 > 裝置連線 > SAS-IoT-Devices**。 記下共用存取簽章 **主要金鑰** 值。

使用 Cloud Shell 從您剛取出的群組 SAS 金鑰產生裝置金鑰：

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

記下產生的裝置金鑰，您稍後將在本教學課程中使用此值。
