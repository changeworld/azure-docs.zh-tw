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
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70049020"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

在本文中，您會建立後端服務，以排程作業在裝置上叫用直接方法、排程作業以更新裝置對應項，以及監視每個工作的進度。 若要執行這些作業，您的服務需要登錄**讀取**和登錄**寫入**許可權。 根據預設，每個 IoT 中樞都會使用名為**registryReadWrite**的共用存取原則來建立，以授與這些許可權。
