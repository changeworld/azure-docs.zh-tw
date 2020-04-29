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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70050424"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

在本文中，您會建立後端服務，將所需的屬性新增至裝置對應項，然後查詢身分識別登錄，以尋找具有已據以更新之報告屬性的所有裝置。 您的服務需要**服務**連線許可權來修改裝置對應項的所需屬性，而且需要登錄**讀取**許可權才能查詢身分識別登錄。 沒有預設的共用存取原則只包含這兩個許可權，因此您必須建立一個。
