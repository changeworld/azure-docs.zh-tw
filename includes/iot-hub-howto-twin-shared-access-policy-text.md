---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050424"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

在本文中，您將創建一個後端服務，將所需屬性添加到設備孿生中，然後查詢標識註冊表以查找報告的屬性已相應更新的所有設備。 服務需要**服務連接**許可權來修改設備孿生所需的屬性，並且它需要**註冊表讀取**許可權來查詢標識註冊表。 沒有預設的共用訪問策略僅包含這兩個許可權，因此您需要創建一個許可權。
