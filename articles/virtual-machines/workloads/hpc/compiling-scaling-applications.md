---
title: 擴展 HPC 應用程式 - Azure 虛擬機器 |微軟文檔
description: 瞭解如何在 Azure VM 上擴展 HPC 應用程式。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707844"
---
# <a name="scaling-hpc-applications"></a>擴展 HPC 應用程式

Azure 上 HPC 應用程式的最佳擴展和橫向擴充性能需要針對特定工作負荷進行性能調整和優化實驗。 本節和 VM 系列特定頁面提供了擴展應用程式的一般指南。

## <a name="compiling-applications"></a>編譯應用程式

儘管不是必需的，但使用適當的優化標誌編譯應用程式可在 HB 和 HC 系列 VM 上提供最佳的放大性能。

### <a name="amd-optimizing-cc-compiler"></a>AMD 優化 C/C++ 編譯器

AMD 優化 C/C++ 編譯器 （AOCC） 編譯器系統提供高水準的高級優化、多執行緒和處理器支援，包括全域優化、向量化、過程間分析、環路轉換和代碼生成。 AOCC編譯器二進位檔案適用于具有GNU C庫（glibc）版本2.17及以上版本的Linux系統。 編譯器套件由 C/C++ 編譯器 （clang）、Fortran 編譯器 （FLANG） 和 Clang 前端（龍蛋）組成。

### <a name="clang"></a>鐺

Clang 是處理預處理、分析、優化、代碼生成、程式集和連結的 C、C++ 和 Objective-C 編譯器。 Clang 支援`-march=znver1`該標誌，以實現 AMD 基於 Zen 的 x86 架構的最佳代碼生成和調優。

### <a name="flang"></a>法朗

FLANG 編譯器是 AOCC 套件（2018 年 4 月添加）的最近添加，目前正在預發佈，供開發人員下載和測試。 基於 Fortran 2008，AMD 擴展了 FLANG 的https://github.com/flangcompiler/flang)GitHub 版本（ 。 FLANG 編譯器支援所有 CLANG 編譯器選項和任意數量的特定于 FLANG 的編譯器選項。

### <a name="dragonegg"></a>龍蛋

DragonEgg 是 gcc 外掛程式，用 LLVM 專案中的優化器和代碼產生器替換。 AOCC 附帶的龍蛋與 gcc-4.8.x 配合使用，已針對 x86-32/x86-64 目標進行了測試，並已在各種 Linux 平臺上成功使用。

GFortran 是 Fortran 程式的實際前端，負責預處理、分析和語義分析，生成 GCC GIMPLE 中間表示 （IR）。 龍蛋是一個GNU外掛程式，插入GFortran編譯流。 它實現了 GNU 外掛程式 API。 使用外掛程式體系結構，DragonEgg 成為編譯器驅動程式，驅動編譯的不同階段。  遵循下載和安裝說明後，可以使用以下語言調用龍蛋： 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 編譯器
PGI社區版 17 已確認與 AMD EPYC 合作。 PGI 編譯的 STREAM 版本確實可提供平臺的完整記憶體頻寬。 較新的社區版 18.10 （2018 年 11 月） 也應同樣工作良好。 下面是使用英特爾編譯器以最佳方式編譯的 CLI 示例：

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>英特爾編譯器
英特爾編譯器 18 已確認與 AMD EPYC 合作。 下面是使用英特爾編譯器以最佳方式編譯器的 CLI 示例。

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 編譯器 
對於 HPC，AMD 建議 GCC 編譯器 7.3 或更新。 不建議使用舊版本（如 RHEL/CentOS 7.4 中包含的 4.8.5）。 GCC 7.3 和更高版本將在 HPL、HPCG 和 DGEMM 測試中提供更高的性能。

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>調整應用程式 

以下建議適用于最佳應用程式擴展效率、性能和一致性：

* 使用順序固定方法（而不是自動平衡方法）將進程固定到內核 0-59。 
* Numa/Core/HwThread 綁定優於預設綁定。
* 對於混合並行應用（OpenMP_MPI），每個 CCX 使用 4 個執行緒和 1 個 MPI 排名。
* 對於純 MPI 應用，試驗 1-4 MPI，每個 CCX 排名最佳性能。
* 某些對記憶體頻寬具有極高敏感性的應用程式可能受益于每個 CCX 使用數量的內核。 對於這些應用程式，每個 CCX 使用 3 或 2 個內核可能會減少記憶體頻寬爭用，並產生更高的實際性能或更一致的可擴充性。 特別是，MPI全減可能受益于這一點。
* 對於更大的比例運行，建議使用 UD 或混合 RC+UD 傳輸。 許多 MPI 庫/運行時庫在內部執行此操作（如 UCX 或 MVAPICH2）。 檢查傳輸配置有無大規模運行。

## <a name="next-steps"></a>後續步驟

在 Azure 上瞭解有關[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多詳細資訊。
