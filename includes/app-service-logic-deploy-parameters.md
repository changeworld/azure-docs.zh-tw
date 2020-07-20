---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "68385752"
---
您可以使用 Azure Resource Manager，針對要在部署範本時使用的值定義參數。 此範本有一個 `parameters` 區段，內含所有參數值。 此範本會使用每個參數值來定義您想要部署的資源。

> [!NOTE]
> 請不要為永遠保持不變的值定義參數。 只針對會隨著您正在部署的專案或要在其中部署的環境而變化的值定義參數。

定義參數時：

* 若要定義使用者可以在部署期間提供的允許值，請使用 **allowedValues** 欄位。

* 若要在部署期間未提供任何值時，將預設值指派給參數，請使用 **defaultValue** 欄位。 
