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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "70050424"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

在本文中，您會建立後端服務，其會將所需的屬性新增至裝置對應項，然後查詢身分識別登錄，以尋找具有所報告屬性且已分別更新的所有裝置。 服務需要有**服務連線**權限，才能修改裝置對應項的所需屬性，也需要有**登錄讀取**權限才能查詢身分識別登錄。 沒有任何預設的共用存取原則只包含這兩項權限，所以必須自己建立一個。
