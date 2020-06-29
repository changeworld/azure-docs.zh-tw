---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70049020"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

在本文中，您會建立後端服務，其會排程在裝置上叫用直接方法的作業、排程更新裝置對應項的作業，以及監視每項作業的進度。 若要執行這些作業，則服務需要有**登錄讀取**和**登錄寫入**權限。 根據預設，每個 IoT 中樞都是使用授與這些權限且名為 **registryReadWrite** 的共用存取原則所建立。
