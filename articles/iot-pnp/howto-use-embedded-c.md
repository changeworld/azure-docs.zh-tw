---
title: 在受限制的裝置上使用 IoT 隨插即用 |Microsoft Docs
description: 瞭解如何使用適用于 Embedded C 或 Azure RTO 的 SDK，在受限的裝置上執行 IoT 隨插即用。
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a5b826bd76eb7e56620bcb5b5eec9464ebacffc6
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579952"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>在受限制的裝置上執行 IoT 隨插即用

如果您要針對 *受限制的裝置*進行開發，您可以搭配使用 IoT 隨插即用與 [適用于內嵌 C IoT 用戶端程式庫](https://aka.ms/embeddedcsdk) 或 [azure rto](https://docs.microsoft.com/azure/rtos/overview-rtos)的 azure SDK。 本文包含這些受限案例的連結和資源。

## <a name="use-the-sdk-for-embedded-c"></a>使用適用于 Embedded C 的 SDK

適用于 Embedded C 的 SDK 提供輕量的解決方案，可將受限的裝置連線至 Azure IoT 服務，包括使用 [IoT 隨插即用慣例](concepts-convention.md)。 下列連結包含適用于實際裝置的範例，以及教育和偵測用途。

### <a name="use-a-real-device"></a>使用真實裝置

如需使用適用于 Embedded C 的 SDK、裝置布建服務，以及在真實裝置上 IoT 隨插即用的完整端對端教學課程，請參閱將 [PIC-Iot Wx 開發板重新規劃，以透過 IoT 中樞裝置布建服務連線至 Azure](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)。

### <a name="introductory-samples"></a>入門範例

適用于 Embedded C 存放庫的 SDK 包含 [數個範例](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) ，示範如何使用 IoT 隨插即用：

> [!NOTE]
> 這些範例會顯示在 Windows 和 Linux 上執行，以供教育和偵測之用。 在生產案例中，這些範例僅適用于受限制的裝置。

- [具有適用于 Embedded C 之 SDK 的控溫器範例](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [使用適用于 Embedded C 的 SDK 的溫度控制器範例](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>使用 Azure RTO

Azure RTO 包含可將原生連線能力新增至 Azure IoT 雲端服務的輕量層。 這一層提供簡單的機制，可讓您在使用 Azure RTO 的 advanced 功能時，將受限的裝置連線至 Azure IoT。

### <a name="toolchains"></a>工具鏈

Azure RTO 範例提供不同類型的 IDE 和工具鏈組合，例如：

- IAR： IAR 的 [內嵌工作臺](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake：建置於開放原始碼 [CMake](https://cmake.org/) 組建系統和 [GNU Arm Embedded 工具鏈](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)之上。
- MCUExpresso： NXP 的 [MCUXPRESSO IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube： STMicroelectronic 的 [STM32CUBE IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB：微晶片的 [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>範例

如需示範如何使用 Azure RTO 和 IoT 隨插即用開始使用不同裝置的範例，請參閱下表：

製造商 | 裝置 | 範例 |
| --- | --- | --- |
| 晶片 | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| >stmicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| >stmicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| >stmicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>後續步驟

既然您已瞭解在受限裝置上執行 IoT 隨插即用的選項，建議的下一個步驟是瞭解 [IoT 隨插即用的慣例](concepts-convention.md)。