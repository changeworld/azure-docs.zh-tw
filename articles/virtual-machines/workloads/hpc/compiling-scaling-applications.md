---
title: 調整 HPC 應用程式-Azure 虛擬機器 |Microsoft Docs
description: 瞭解如何在 Azure Vm 上調整 HPC 應用程式。
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 7e05d64420cd920242a887b206fd38cd4a655509
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967001"
---
# <a name="scaling-hpc-applications"></a>調整 HPC 應用程式

在 Azure 上進行 HPC 應用程式的最佳向上延展和向外延展效能，需要針對特定工作負載進行效能微調和優化實驗。 本節和 VM 系列專用頁面提供調整應用程式的一般指引。

## <a name="compiling-applications"></a>編譯應用程式

雖然並非必要，但使用適當的優化旗標編譯應用程式，可在 HB 和 HC 系列 Vm 上提供最佳的擴大效能。

### <a name="amd-optimizing-cc-compiler"></a>AMD 優化 C/c + + 編譯器

AMD 優化 C/c + + 編譯器 (AOCC) 編譯器系統提供高層級的 advanced 優化、多重執行緒和處理器支援，其中包括全域優化、向量化、程式內部程式分析、迴圈轉換和程式碼產生。 AOCC 編譯器二進位檔適用于具有 GNU C 程式庫 (glibc) 2.17 版和更新版本的 Linux 系統。 編譯器套件包含 C/c + + 編譯器 (clang) 、Fortran 編譯器 (FLANG) 以及 Fortran 前端以 Clang (的中) 。

### <a name="clang"></a>鐺

Clang 是 C、c + + 和目標 C 編譯器處理前置處理、剖析、優化、程式碼產生、元件和連結。 Clang 支援旗標，  `-march=znver1` 以針對 AMD 的 Zen 型 x86 架構啟用最佳程式碼產生和微調。

### <a name="flang"></a>FLANG

FLANG 編譯器是 AOCC suite 的最新加入， (在2018年4月新增) ，而且目前是發行前版本，可供開發人員下載和測試。 基於 Fortran 2008，AMD 擴充了 GitHub 版本的 FLANG (https://github.com/flang-compiler/flang) 。 FLANG 編譯器支援所有 Clang 編譯器選項，以及額外的 FLANG 特定編譯器選項數目。

### <a name="dragonegg"></a>DragonEgg

DragonEgg 是一種 gcc 外掛程式，可將 GCC 的優化工具和程式碼產生器取代為 LLVM 專案中的程式碼產生器。 AOCC 隨附的 DragonEgg 可與 gcc 4.8 搭配使用，並已針對 x86-32/x86-64 目標進行測試，並已在各種 Linux 平臺上成功使用。

GFortran 是 Fortran 計畫的實際前端，負責前置處理、剖析和語義分析，以產生 GCC GIMPLE 中繼表示 (IR) 。 DragonEgg 是一種 GNU 外掛程式，可插入 GFortran 編譯流程。 它會實現 GNU 外掛程式 API。 使用外掛程式架構時，DragonEgg 會成為編譯器驅動程式，進而驅動編譯的不同階段。  遵循下載和安裝指示之後，您可以使用下列命令來叫用「中」： 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 編譯器
PGI 社區版。 已確認17與 AMD EPYC 搭配運作。 PGI 編譯的資料流程版本提供平臺的完整記憶體頻寬。 較新的版本 18.10 (11 月 2018) 同樣可以正常運作。 以下是以最佳方式使用 Intel 編譯器進行編譯器的 CLI 範例：

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel 編譯器
Intel 編譯器 ver。 已確認18與 AMD EPYC 搭配運作。 以下是以最佳方式使用 Intel 編譯器進行編譯器的 CLI 範例。

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 編譯器 
針對 HPC，AMD 建議 GCC 編譯器7.3 或更新版本。 不建議使用較舊的版本，例如 RHEL/CentOS 7.4 隨附的4.8.5。 GCC 7.3 和更新版本會在 HPL、HPCG 和 DGEMM 測試上提供明顯更高的效能。

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>調整應用程式 

下列建議適用于最佳應用程式調整效率、效能和一致性：

* 使用連續的固定 (方法將進程釘選到核心0-59，而不是) 的自動平衡方法。 
* Numa/Core/HwThread 的系結比預設系結更好。
* 針對 (OpenMP + MPI) 的混合式平行應用程式，請針對每個 CCX 使用4個執行緒和1個 MPI 等級。
* 對於單純的 MPI 應用程式，請針對每個 CCX 進行 1-4 MPI 排名以獲得最佳效能。
* 有些應用程式對於記憶體頻寬有極大的效用，可能會因為每個 CCX 使用的核心數目減少而受益。 針對這些應用程式，每個 CCX 使用3或2個核心可能會減少記憶體頻寬爭用，並產生更高的真實世界效能或更一致的擴充性。 尤其是 MPI Allreduce 可從中獲益。
* 針對大幅較大的擴展執行，建議使用 UD 或混合式 RC + UD 傳輸。 許多 MPI 程式庫/執行時間程式庫會在內部 (，例如 UCX 或 MVAPICH2) 。 檢查大規模執行的傳輸設定。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 上的 [HPC](/azure/architecture/topics/high-performance-computing/) 。
