---
title: 調整 HPC 應用程式-Azure 虛擬機器 |Microsoft Docs
description: 瞭解如何在 Azure Vm 上調整 HPC 應用程式。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707844"
---
# <a name="scaling-hpc-applications"></a>調整 HPC 應用程式

Azure 上 HPC 應用程式的最佳相應增加和相應放大效能，需要針對特定工作負載進行效能微調和優化實驗。 本節和 VM 系列特定頁面提供調整應用程式的一般指引。

## <a name="compiling-applications"></a>編譯應用程式

雖然不需要，但以適當的優化旗標編譯應用程式，可在 HB 和 HC 系列 Vm 上提供最佳的向上延展效能。

### <a name="amd-optimizing-cc-compiler"></a>AMD 優化 C/c + + 編譯器

AMD 優化 C/c + + 編譯器（AOCC）編譯器系統提供高階的先進優化、多執行緒和處理器支援，其中包含全域優化、向量化、程式內部分析、迴圈轉換和程式碼產生。 AOCC 編譯器二進位檔適用于具有 GNU C 程式庫（glibc）2.17 版和更新版本的 Linux 系統。 編譯器套件包含 C/c + + 編譯器（clang）、Fortran 編譯器（FLANG）和 Fortran 前端 to Clang （龍蛋）。

### <a name="clang"></a>Clang

Clang 是 C、c + + 和以目標為 C 的編譯器，處理前置處理、剖析、優化、程式碼產生、元件和連結。 Clang 支援`-march=znver1`旗標，以針對以 AMD Zen 為基礎的 x86 架構，啟用最佳程式碼產生和微調。

### <a name="flang"></a>FLANG

FLANG 編譯器是 AOCC suite 的最近新增（2018年4月），目前處於發行前版本，供開發人員下載和測試。 根據 Fortran 2008，AMD 擴充了 GitHub 版本的 FLANG （https://github.com/flangcompiler/flang)。 FLANG 編譯器支援所有的 Clang 編譯器選項，以及額外數目的 FLANG 特定編譯器選項。

### <a name="dragonegg"></a>DragonEgg

DragonEgg 是一個 gcc 外掛程式，可將 GCC 的優化工具和程式碼產生器取代為 LLVM 專案中的。 AOCC 隨附的 DragonEgg 適用于 gcc-4.8. x，已針對 x86-32/x86-64 目標進行測試，並已成功在各種 Linux 平臺上使用。

GFortran 是 Fortran 程式的實際前端，負責處理、剖析和產生 GCC GIMPLE 中繼標記法（IR）的語義分析。 DragonEgg 是 GNU 外掛程式，可插入 GFortran 編譯流程。 它會實行 GNU 外掛程式 API。 使用外掛程式架構，DragonEgg 會成為編譯器驅動程式，以驅動不同的編譯階段。  遵循下載和安裝指示之後，可以使用來叫用龍蛋： 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 編譯器
PGI 社區版本。 17已確認可與 AMD EPYC 搭配使用。 PGI 編譯的資料流程版本會提供平臺的完整記憶體頻寬。 較新的「社區版本18.10 （11月2018）」應該同樣可行。 以下是使用 Intel 編譯器優化編譯器的 CLI 範例：

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel 編譯器
Intel 編譯器。 18已確認可與 AMD EPYC 搭配使用。 以下是使用 Intel 編譯器進行優化的編譯器範例 CLI。

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 編譯器 
針對 HPC，AMD 建議 GCC 編譯器7.3 或更新版本。 不建議使用較舊的版本，例如 RHEL/CentOS 7.4 隨附的4.8.5。 GCC 7.3 和更新版本，可在 HPL、HPCG 和 DGEMM 測試上提供明顯更高的效能。

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>調整應用程式 

下列建議適用于最佳應用程式調整效率、效能和一致性：

* 使用連續的固定方法（相對於自動平衡方法），將處理常式釘選到核心0-59。 
* Numa/Core/HwThread 的系結優於預設系結。
* 針對混合式平行應用程式（OpenMP + MPI），請針對每個 CCX 使用4個執行緒和1個 MPI 順位。
* 針對純 MPI 應用程式，請針對每個 CCX 進行 1-4 MPI 順位的實驗，以獲得最佳效能。
* 有些應用程式對記憶體頻寬有極大的敏感性，可能會因為每個 CCX 使用的核心數減少而受益。 針對這些應用程式，每個 CCX 使用3或2個核心可能會降低記憶體頻寬爭用，並產生更高的真實世界效能或更一致的擴充性。 特別是，MPI Allreduce 可能受益于此。
* 針對大幅增加的規模執行，建議使用 UD 或混合式 RC + UD 傳輸。 許多 MPI 程式庫/執行時間程式庫會在內部執行此動作（例如 UCX 或 MVAPICH2）。 檢查大規模執行的傳輸設定。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 上的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 。
