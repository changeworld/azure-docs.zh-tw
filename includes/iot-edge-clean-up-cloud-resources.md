---
title: 包含檔案
description: 包含檔案
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: c1e5027b192f32b50af10a2bdfbadfeb123b39ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174095"
---
### <a name="delete-azure-resources"></a>刪除 Azure 資源 

刪除 Azure 資源和資源群組是無法回復的動作。 請確定您不會誤刪錯誤的資源群組或資源。 如果您在現有的資源群組內建立了 IoT 中樞，而該群組中包含您想要保留的資源，則您只需刪除 IoT 中樞資源本身即可，而不要刪除資源群組。

若要刪除資源：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [資源群組]  。

2. 選取您的 IoT Edge 測試資源所屬的資源群組名稱。 

3. 檢閱您的資源群組中包含的資源清單。 若要將其全部刪除，您可以選取 [刪除資源群組]  。 如果只要刪除某些部分，您可以按一下各個資源將其個別刪除。 
