---
title: 系統需求
description: 列出 Azure 遠端呈現的系統要求
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 8573a88d5371bbde07a541c789f52e6c44f1e279
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411137"
---
# <a name="system-requirements"></a>系統需求

> [!IMPORTANT]
> **Azure 遠端轉譯**目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本章列出了使用*Azure 遠端呈現*(ARR) 的最低系統要求。

## <a name="development-pc"></a>開發 PC

* Windows 10 版本 1903 或更高版本。
* 最新的圖形驅動程式。
* 可選:如果要使用遠端渲染內容的本地預覽(例如 Unity),則 H265 硬體視頻解碼器。

> [!IMPORTANT]
> Windows 更新並不總是提供最新的 GPU 驅動程式,請檢視 GPU 製造商的網站以取得最新的驅動程式:
>
> * [AMD 驅動程式](https://www.amd.com/en/support)
> * [英特爾驅動程式](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA 驅動程式](https://www.nvidia.com/Download/index.aspx)

下表列出了哪些 GPU 支援 H265 硬體視頻解碼。

| GPU 製造商 | 支援的模型 |
|-----------|:-----------|
| NVIDIA | 檢視[這個頁面底部的](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix) **NVDEC 支援矩陣**。 您的 GPU 需要在**H.265 4:2:0 8 位**列中使用 YES。 |
| AMD | GPU 與至少版本6的AMD[的統一視訊解碼器](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)。 |
| Intel | 天湖與較新的CPU |

即使可能安裝了正確的 H265 編解碼器,編解碼器 DLL 上的安全屬性也可能導致編解碼器初始化失敗。 [故障排除指南](../resources/troubleshoot.md#h265-codec-not-available)介紹了如何解決此問題的步驟。 DLL 問題只能在桌面應用程式中使用服務時發生,例如在 Unity 中。

## <a name="devices"></a>裝置

Azure 遠端呈現目前僅支援**HoloLens 2**和 Windows 桌面作為目標設備。 請參閱[平臺限制](../reference/limits.md#platform-limitations)部分。

使用最新的 HEVC 編解碼器非常重要,因為較新版本的延遲有顯著改善。 要檢查裝置上安裝了哪個版本:

1. 啟動**微軟商店**。
1. 按下右上角的 **"..."** 按鈕。
1. 選擇**下載與更新**。
1. 從裝置製造商搜尋**HEVC 視訊延伸的清單**。
1. 確保列出的編解碼器至少具有版本**1.0.21821.0**。
1. 按下「**獲取更新**」按鈕並等待安裝。

## <a name="network"></a>網路

穩定、低延遲的網路連接對於良好的用戶體驗至關重要。

有關[網路要求](../reference/network-requirements.md),請參閱專用章節。

有關解決網路問題的疑難解答,請參閱[故障排除指南](../resources/troubleshoot.md#unstable-holograms)。

## <a name="software"></a>軟體

必須安裝下列軟體：

* 最新版本的**可視化工作室 2019** [(下載)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Windows SDK 10.0.18362.0(**[下載)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT(**[下載 )](https://git-scm.com/downloads)
* 可選:要從桌面 PC 上的伺服器查看影片流,您需要**HEVC 視頻擴展(Microsoft** [應用商店連結)。](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)

## <a name="unity"></a>Unity

對使用 Unity 開發,請安裝

* Unity 2019.3.1 [(下載)](https://unity3d.com/get-unity/download)
* 在 Unity 中安裝下列模組：
  * **UWP** - 通用 Windows 平台建置支援
  * **IL2CPP** - Windows 建置支援 (IL2CPP)

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Unity 轉譯模型](../quickstarts/render-model.md)
