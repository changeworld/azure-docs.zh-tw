---
title: Azure IoT 中樞裝置布建服務-裝置概念
description: '描述 (DPS Azure IoT 中樞裝置布建服務的裝置重新布建概念) '
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9653a584382584d982c55008a6e8547de28691b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842847"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT 中樞裝置重新佈建概念

在 IoT 解決方案的生命週期中，在 IoT 中樞之間移動裝置是很常見的。 移動原因可能包括下列案例：

* **地理位置/地理延遲**：當裝置在位置之間移動時，可透過將裝置移轉至較近的 IoT 中樞以改善網路延遲。

* **多租用戶**：裝置可能會在相同的 IoT 解決方案內使用，並重新指派給新客戶或客戶網站。 系統可能會使用不同的 IoT 中樞為這個新客戶提供服務。

* **解決方案變更**：裝置可移至新的或已更新的 IoT 解決方案。 此重新指派可能需要裝置與連線至其他後端元件的新 IoT 中樞通訊。

* **隔離**：類似於解決方案變更。 故障、遭入侵或已過期的裝置可能會重新指派給只能更新，讓裝置再次符合合規性要求的 IoT 中樞。 在裝置正常運作之後，就會移轉回其主要中樞。

裝置佈建服務內的重新佈建支援可解決這些需求。 裝置可以根據其註冊項目中設定的重新佈建原則，自動重新指派給新的 IoT 中樞。

## <a name="device-state-data"></a>裝置狀態資料

裝置狀態資料是由 [裝置](../iot-hub/iot-hub-devguide-device-twins.md) 對應項和裝置功能所組成。 此資料儲存在裝置佈建服務執行個體與裝置指派後所屬的 IoT 中樞中。

![此圖顯示布建如何搭配裝置布建服務運作。](./media/concepts-device-reprovisioning/dps-provisioning.png)

最初使用裝置佈建服務執行個體佈建裝置時，會執行下列步驟：

1. 裝置會向裝置佈建服務執行個體傳送佈建要求。 服務執行個體會根據註冊項目驗證裝置身分識別，並建立裝置狀態資料的初始設定。 服務執行個體會根據註冊設定將裝置指派給 IoT 中樞，然後將該 IoT 中樞指派傳回給裝置。

2. 佈建服務執行個體會將任何初始裝置狀態資料的複本提供給指派的 IoT 中樞。 裝置會連線到指派的 IoT 中樞並開始運作。

經過一段時間之後，[裝置作業](../iot-hub/iot-hub-devguide-device-twins.md#device-operations)與[後端作業](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations)可能會更新 IoT 中樞上的裝置狀態資料。 裝置佈建服務執行個體中儲存的初始裝置狀態資訊則會維持不變。 此維持不變的裝置狀態資料為初始設定。

![使用裝置佈建服務佈建](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

視情況而定，在 IoT 中樞之間移動裝置時，可能也需要將前一個 IoT 中樞上更新的裝置狀態移轉到新的 IoT 中樞。 裝置佈建服務中的重新佈建原則支援此移轉。

## <a name="reprovisioning-policies"></a>重新佈建原則

根據情況，裝置通常會在重新開機時，將要求傳送至佈建服務執行個體。 它也支援依需求手動觸發佈建的方法。 註冊項目上的重新佈建原則會決定裝置佈建服務執行個體如何處理這些佈建要求。 此原則也會決定是否應該在重新佈建期間移轉裝置狀態資料。 相同原則可供個別註冊與註冊群組使用：

* **重新佈建和移轉資料**：此原則為新註冊項目的預設值。 此原則會在與註冊項目關聯的裝置提交新的要求 (1) 時採取動作。 視註冊項目設定而定，裝置可能會重新指派給其他 IoT 中樞。 如果裝置所屬的 IoT 中樞有所變更，將會移除初始 IoT 中樞中的裝置註冊。 該初始 IoT 中樞中已更新的裝置狀態資訊將會移轉至新的 IoT 中樞 (2)。 在遷移期間，裝置的狀態將會回報為 [ **指派**]。

    ![此圖顯示當與註冊專案關聯的裝置提交新的要求時，原則會採取動作。](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **重新佈建並重設為初始設定**：此原則會在與註冊項目關聯的裝置提交新的佈建要求 (1) 時採取動作。 視註冊項目設定而定，裝置可能會重新指派給其他 IoT 中樞。 如果裝置所屬的 IoT 中樞有所變更，將會移除初始 IoT 中樞中的裝置註冊。 系統會將佈建服務執行個體在佈建裝置時收到的初始設定資料提供給新的 IoT 中樞 (2)。 在遷移期間，裝置的狀態將會回報為 [ **指派**]。

    此原則通常會用於在不變更 IoT 中樞的情況下，恢復出廠預設值。

    ![此圖顯示當與註冊專案關聯的裝置提交新的布建要求時，原則如何採取動作。](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **永不重新佈建**：裝置永遠不會重新指派給其他中樞。 提供此原則的目的是為了管理回溯相容性。

### <a name="managing-backwards-compatibility"></a>管理回溯相容性

在 2018 年 9 月之前，將裝置指派給 IoT 中樞的行為具有黏性。 也就是當裝置透過佈建程序返回時，只會指派給相同的 IoT 中樞。

對於與此行為相依的解決方案而言，佈建服務包括回溯相容性。 目前會依據以下條件針對裝置維持此行為：

1. 裝置會與裝置佈建服務中提供原生重新佈建支援之前的 API 版本連線。 請參閱下面的 API 表格。

2. 裝置的註冊項目未設定裝置重新佈建原則。

此相容性可確保先前部署的裝置會經歷與初始測試期間相同的行為。 若要保留先前的行為，請不要將重新佈建原則儲存到這些註冊中。 如果設定了重新佈建原則，重新佈建原則的優先順序會高於該行為。 透過允許重新佈建原則取得優先順序，客戶就可以在不需要為裝置重新安裝映像的情況下更新裝置行為。

下列流程圖可協助您在行為出現時顯示：

![回溯相容性流程圖](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

下表顯示裝置佈建服務中提供原生重新佈建支援之前的 API 版本：

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 與先前版本](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 與先前版本](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 與先前版本](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 或先前版本](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 或先前版本](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 或先前版本](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> 這些值與連結可能會變更。 這只是嘗試判斷客戶可判斷之版本與預期版本的預留位置。

## <a name="next-steps"></a>後續步驟

* [如何重新布建裝置](how-to-reprovision.md)
