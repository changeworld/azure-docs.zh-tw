---
title: Azure Kinect 感應器 SDK 系統需求
description: 瞭解 Windows 和 Linux 上的 Azure Kinect 感應器 SDK 的系統需求。
author: tesych
ms.author: tesych
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/12/2020
ms.topic: article
keywords: azure，kinect，系統需求，CPU，GPU，USB，設定，安裝，最低需求
ms.openlocfilehash: 5cf313114b62532ee3f2b3d7a5142f79218954c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276544"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Azure Kinect 感應器 SDK 系統需求

本檔提供安裝感應器 SDK 以及成功部署 Azure Kinect DK 所需之系統需求的詳細資料。

## <a name="supported-operating-systems-and-architectures"></a>支援的作業系統和架構

- Windows 10 2018 年4月（版本1803、OS 組建17134） release （x64）或更新版本
- Linux Ubuntu 18.04 （x64），包含使用 OpenGLv 4.4 或更新版本的 GPU 驅動程式

感應器 SDK 適用于原生 C/c + + Windows 應用程式的 Windows API （Win32）。 此 SDK 目前不適用於 UWP 應用程式。 Windows 10 的 S 模式不支援 Azure Kinect DK。

## <a name="development-environment-requirements"></a>開發環境需求

若要參與感應器 SDK 開發，請造訪[GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK)。

## <a name="minimum-host-pc-hardware-requirements"></a>最低主機電腦硬體需求

電腦主機硬體需求取決於主機電腦上執行的應用程式/演算法/感應器畫面播放速率/解析度。 適用于 Windows 的建議最小感應器 SDK 設定為：

- 第七代 Intel &reg; CoreTM I3 處理器（具有 HD620 GPU 或更快的雙核心 2.4 GHz）
- 4 GB 記憶體
- 專用的 USB3 埠
- OpenGL 4.4 或 DirectX 11.0 的圖形驅動程式支援

較低的端或較舊的 Cpu 可能也會根據您的使用案例來工作。

Windows/Linux 作業系統和使用中的圖形驅動程式之間的效能也不同。

## <a name="body-tracking-host-pc-hardware-requirements"></a>主體追蹤主機電腦硬體需求

主體追蹤電腦主機需求比一般電腦主機需求更嚴格。 Windows 建議的最小主體追蹤 SDK 設定為：

- 第七代 Intel &reg; CoreTM I5 處理器（四核心 2.4 GHz 或更快）
- 4 GB 記憶體
- NVIDIA GEFORCE GTX 1070 或更佳
- 專用的 USB3 埠

建議的最小設定會假設30fps 追蹤5人的 K4A_DEPTH_MODE_NFOV_UNBINNED 深度模式。 較低的結束或較舊的 Cpu 和 NVIDIA Gpu 也可以根據您的使用案例來執行。

## <a name="usb3"></a>USB3

USB 主機控制器有已知的相容性問題。 您可以在[疑難排解頁面](troubleshooting.md#usb3-host-controller-compatibility)中找到詳細資訊

## <a name="next-steps"></a>後續步驟

- [Azure Kinect DK 總覽](about-azure-kinect-dk.md)

- [設定 Azure Kinect DK](set-up-azure-kinect-dk.md)

- [設定 Azure Kinect body 追蹤](body-sdk-setup.md)
