---
title: 與先前版本之間的差異
titleSuffix: Azure Digital Twins
description: 瞭解 Azure Digital Twins 的新版本的變更內容
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 8a69e0bfe172d2cc7a291ee51ffa73ae7b484731
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613291"
---
# <a name="how-is-the-new-azure-digital-twins-different-from-the-previous-version-2018"></a>新版 Azure Digital Twins 與先前版本 (2018) 有何不同？

Azure Digital Twins 的第一個公開預覽版本已於 2018 年 10 月發行。 雖然先前版本的核心概念已在公開預覽中傳遞至新的服務，但許多介面與實作詳細資料都已經變更，讓服務更具彈性且更容易存取。 這些變更都是受到客戶的意見反應啟發所形成的。

> [!IMPORTANT]
> 在新服務的擴充功能中，先前的 Azure Digital Twins 服務將於2020 年年底淘汰。

如果您在先前的公開預覽期間使用第一版的 Azure Digital Twins，請使用本文提供的資訊和最佳作法來瞭解如何使用新的服務，並善用其功能。

## <a name="differences-by-topic"></a>依主題區分的差異

以下圖表提供在舊版服務和新版 (現行) 服務之間已變更之概念的並排檢視。

| 主題 | 在舊版本中 | 在新版本中 |
| --- | --- | --- | --- |
| **模型化**<br>*更有彈性* | 先前的版本是以智慧空間為基礎所設計，因此隨附建築物的內建詞彙。 | 新的 Azure Digital Twins 是無從驗證網域的。 您可以為自己的解決方案定義專屬的自訂詞彙和自訂模型，以更有彈性的方式呈現更多類型的環境。<br><br>深入瞭解，請參閱 [概念：自訂模型](concepts-models.md)。 |
| **拓撲**<br>*更有彈性*| 先前的版本支援針對智慧空間量身打造的樹狀資料結構。 數位分身已使用階層式關聯性進行連線。 | 藉由新版本，您的數位分身可以連線到任意的圖表拓撲，並以您想要的方式組織。 這可讓您更有靈活地表達現實環境的複雜關聯性。<br><br>深入瞭解，請參閱 [概念：數位分身和對應項圖表](concepts-twins-graph.md)。 |
| **計算**<br>*內容更豐富、更有彈性* | 在先前的版本中，處理事件和遙測的邏輯是在 JavaScript 使用者定義函式 (UDF) 中加以定義的。 使用 UDF 進行的偵錯受到限制。 | 新版本具有開放式計算模型：您可以藉由附加外部計算資源 (例如 [Azure Functions](../azure-functions/functions-overview.md)) 來提供自訂邏輯。 這可讓您使用自己選擇的程式設計語言、不受限制地存取自訂的程式碼資料庫，以及利用外部服務可能擁有的開發與偵測資源。<br><br>深入瞭解，請參閱[操作說明：設定用來處理資料的 Azure 函式](how-to-create-azure-function.md)。 |
| **使用 IoT 中樞進行裝置管理**<br>*更便於存取* | 先前發行的受控裝置，以及 Azure Digital Twins 服務內部的 [IoT 中樞](../iot-hub/about-iot-hub.md) 執行個體。 開發人員無法完整存取此整合式中樞。 | 在新版本中，您會藉由附加獨立建立的 IoT 中樞執行個 (以及其已管理的任何裝置) 來「自備」 IoT 中樞。 這可讓您完整存取 IoT 中樞的功能，並讓您控制裝置管理。<br><br>深入瞭解，請參閱[操作說明：從 IoT 中樞內嵌遙測](how-to-ingest-iot-hub-data.md)。 |
| **安全性**<br>*更多標準* | 先前的版本具有預先定義的角色，您可以用來管理執行個體的存取權。 | 新版本會與其他 Azure 服務所使用的相同 Azure [角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 後端服務整合。 這可讓您以更簡便的方式，在解決方案中的其他 Azure 服務之間進行驗證，例如 IoT 中樞、Azure Functions、事件方格等等。<br>使用 RBAC，您仍然可以使用預先定義的角色，也可以建立和設定自訂角色。<br><br>深入瞭解，請參閱 [概念：Azure Digital Twins 解決方案的安全性](concepts-security.md)。 |
| **延展性**<br>*大於* | 先前的版本具有裝置、訊息、圖表和縮放單位的調整限制。 每個訂閱僅支援一個 Azure Digital Twins 執行個體。  | 新版本仰賴新的結構，並具有改良的可擴縮性，並更具計算能力。 新版本也支援每個訂閱每個區域 10 個執行個體。<br><br>請參閱 [參考：公開預覽服務的限制](reference-service-limits.md)，以取得公開預覽中限制的詳細資料。 |

## <a name="service-limits-in-public-preview"></a>公開預覽中的服務限制

如需此公開預覽期間的 Azure Digital Twins 限制清單，請參閱 [參考：公開預覽服務限制](reference-service-limits.md)。

## <a name="next-steps"></a>後續步驟

接下來，藉由第一個教學課程深入瞭解 Azure Digital Twins 的使用方式：

> [!div class="nextstepaction"]
> [教學課程：撰寫用戶端應用程式的程式碼](tutorial-code.md)