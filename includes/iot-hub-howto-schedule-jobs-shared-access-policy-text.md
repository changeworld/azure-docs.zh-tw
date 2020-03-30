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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70049020"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

在本文中，您將創建一個後端服務，其中計畫作業以在設備上調用直接方法，計畫作業以更新設備孿生，並監視每個作業的進度。 要執行這些操作，服務需要**註冊表讀取**和**註冊表寫入**許可權。 預設情況下，每個 IoT 中心都使用名為**註冊表 ReadWrite**的共用訪問策略創建，該策略授予這些許可權。
