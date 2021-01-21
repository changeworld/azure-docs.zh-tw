---
title: Windows 上適用于 Linux 的 Azure IoT Edge |Microsoft Docs
description: 您可以在 Windows 10 裝置上執行 Linux IoT Edge 模組的總覽
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: ebe0ac1151f3a1f43072f2832e2f433182ccc82d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633957"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Windows (Preview 上的 Linux Azure IoT Edge 的內容) 

適用于 Windows 的 Linux Azure IoT Edge 可讓您在 Windows IoT 部署中執行容器化 Linux 工作負載以及 Windows 應用程式。 依賴 Windows IoT 來增強 edge 裝置的企業，現在可以利用 Linux 中建的雲端原生分析解決方案。

適用于 Windows 的 Linux IoT Edge 可在 Windows 裝置上執行 Linux 虛擬機器。 Linux 虛擬機器隨 IoT Edge 執行時間預先安裝。 部署至裝置的任何 IoT Edge 模組都會在虛擬機器內執行。 同時，Windows 主機裝置上執行的 Windows 應用程式可以與在 Linux 虛擬機器中執行的模組進行通訊。

>[!NOTE]
>請考慮採用我們的 [產品問卷](https://aka.ms/AzEFLOW-Registration) ，以協助我們根據您 IoT Edge 的背景和目標，來改善 Windows 上的 Linux Azure IoT Edge。 您也可以使用此問卷來註冊未來的 Windows 公告 Azure IoT Edge for Linux。

## <a name="components"></a>單元

Windows 上適用于 Linux 的 IoT Edge 會使用下列元件，讓 Linux 和 Windows 工作負載彼此執行並順暢地通訊：

* 執行 **Azure IoT Edge 的 linux 虛擬機器**：以 Microsoft 的第一方 [Mariner](https://github.com/microsoft/CBL-Mariner)作業系統為基礎的 linux 虛擬機器，是使用 IoT Edge 執行時間所建立，並驗證為 IoT Edge 工作負載的第1層支援環境。

* **Windows Admin Center**：適用于 Windows Admin Center 的 IoT Edge 擴充功能可協助您在 Linux 虛擬機器上進行 IoT Edge 的安裝、設定和診斷。 Windows Admin Center 可以在本機裝置上的 Windows 上部署適用于 Linux 的 IoT Edge，也可以連線到目標裝置，並從遠端系統管理它們。

* **Microsoft Update**：與 Microsoft Update 的整合可讓 Windows 執行時間元件、MARINER Linux VM 和 IoT Edge 保持在最新狀態。

![Windows 和 Linux VM 會以平行方式執行，而 Windows Admin Center 控制兩個元件](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Windows 進程與 Linux 虛擬機器之間的雙向通訊表示 Windows 進程可以針對在 Linux 容器中執行的工作負載提供使用者介面或硬體 proxy。

## <a name="samples"></a>範例

Windows 上適用于 Linux 的 IoT Edge 強調 Linux 和 Windows 元件之間的互通性。

如需示範 Windows 應用程式與 IoT Edge 模組之間通訊的範例，請參閱 [Windows 10 IoT 範例](https://github.com/microsoft/Windows-IoT-Samples)。

## <a name="public-preview"></a>公開預覽

Windows 上適用于 Linux 的 IoT Edge 目前處於 [公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。 安裝和管理程式可能會與正式運作的功能不同。

目前，Windows 上的 Linux IoT Edge 會使用 Windows Admin Center 的 Windows Insider Preview 版本。 如需有關 Windows 測試人員計畫以及註冊的詳細資訊，請參閱 [Windows 測試人員計畫是什麼？](https://insider.windows.com/about-windows-insider-program)。

## <a name="support"></a>支援

使用 IoT Edge 支援和意見反應通道來取得 Windows 上 Linux IoT Edge 的協助。

**報告 bug** -可在 IoT Edge 開放原始碼專案的 [ [問題] 頁面](https://github.com/azure/iotedge/issues) 上報告錯誤。 

**Microsoft 客戶支援小組** – 具有 [支援方案](https://azure.microsoft.com/support/plans/)的任何使用者都可以接從 [Azure 入口網站](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)建立支援票證，與 Microsoft 客戶支援小組互動。

**功能要求** – Azure IoT Edge 產品會透過產品的 [User Voice 頁面](https://feedback.azure.com/forums/907045-azure-iot-edge)來追蹤功能要求。

## <a name="next-steps"></a>下一步

如需詳細資訊和操作範例，請觀看 [Windows 10 IoT 企業版的 Linux IoT Edge](https://aka.ms/EFLOWPPC9) 。

遵循在 [windows 裝置上安裝和](how-to-install-iot-edge-on-windows.md) 布建 linux 的 Azure IoT Edge 中的步驟，以在 windows 上使用適用于 linux 的 IoT Edge 來設定裝置。
