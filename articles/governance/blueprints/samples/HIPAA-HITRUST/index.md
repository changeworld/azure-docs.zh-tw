---
title: HIPAA HITRUST 基礎藍圖範例概觀
description: HIPAA HITRUST 藍圖範例的概觀。 此藍圖範例可協助客戶評定特定 HIPAA HITRUST 控制項。
ms.date: 12/3/2019
ms.topic: sample
ms.openlocfilehash: ebe907053b37ebe0ff99248aa181a480c045d533
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "75470061"
---
# <a name="overview-of-the-hipaa-hitrust-blueprint-sample"></a>HIPAA HITRUST 藍圖範例的概觀

HIPAA HITRUST 藍圖範例使用 [Azure 原則](../../../policy/overview.md)提供了一組治理規範，協助您取得 [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) 證明。 

## <a name="blueprint-sample"></a>藍圖範例

這個藍圖範例可協助客戶針對任何需要資格認證，或符合 HITRUST HIPAA 架構的已部署 Azure 的架構，部署一組核心原則。 [控制項對應](./control-mapping.md)區段可針對此方案中包含的原則提供詳細資料，並說明這些原則如何滿足由 HITRUST HIPAA 架構定義的各種控制項。 將資源指派給架構時，Azure 原則會評估符合已指派原則的資源。

## <a name="next-steps"></a>後續步驟

您已檢閱了 HIPAA HITRUST 藍圖範例的概觀。 接下來，請瀏覽下列文章，以深入了解控制項對應及部署此範例的方法：

> [!div class="nextstepaction"]
> [HIPAA HITRUST 藍圖 - 控制項對應](./control-mapping.md)
> [HIPAA HITRUST 藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。