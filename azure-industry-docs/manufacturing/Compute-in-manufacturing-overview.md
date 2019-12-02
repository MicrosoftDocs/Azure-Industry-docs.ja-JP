---
title: 製造業向けの高性能なオンデマンド Azure コンピューティング サービス
author: ercenk
ms.author: ercenk
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 製造業界における高性能コンピューティングのニーズの概要。
ms.openlocfilehash: fe5200a726b2a65efaed2bc7a8de01e97766d425
ms.sourcegitcommit: 2714a77488c413f01beb169a18acab45663bcfd7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308519"
---
# <a name="on-demand-scalable-high-power-compute"></a>オンデマンドのスケーラブルな高性能コンピューティング

## <a name="introduction"></a>はじめに

顧客は、軽量、強固、安全、持続可能、カスタマイズ済みという特性を兼ね備えた製品を求めています。 そのため、設計段階はますます複雑化しています。 この段階では、コンピューターを使用して、視覚化、分析、シミュレーション、最適化が行われます。 これらのタスクは高度化していき、大量の計算を必要とするようになります。 これに加えて、製品がますます接続され、処理と分析を必要とする膨大な量のデータが生成されています。

このすべてが合わさって、オンデマンドの大規模なコンピューティング リソースという 1 つのニーズになっています。

この記事では、大きな処理能力を必要とする、エンジニアリングおよび製造の既知の分野と、Microsoft Azure プラットフォームによる支援について説明します。

## <a name="cloud-design-workstations"></a>クラウド設計ワークステーション

製品設計者は、製品開発ライフサイクルの設計フェーズと計画フェーズでさまざまなソフトウェア ツールを使用します。 CAD ツールには、設計者のワークステーション上の強力なグラフィックス機能が必要ですが、これらのワークステーションはコストがかかります。 通常、このような高性能ワークステーションは設計者のオフィス内にあり、その場所に物理的に結び付けられています。

クラウド ソリューションの普及が進み、新しい機能が利用可能になるにつれて、クラウド ワークステーションの概念が現実味を帯びてきました。 クラウドでホストされているワークステーションを使用すると、設計者はどこからでもアクセスできます。 また、組織は、コスト モデルを設備投資から運用コストに変更することが可能になります。

### <a name="remote-desktop-protocol"></a>リモート デスクトップ プロトコル

Microsoft のリモート デスクトップ プロトコル (RDP) では、長い間、TCP のみがサポートされてきました。 TCP では、UDP よりも多くのオーバーヘッドが発生します。 RDP 8.0 以降、Microsoft リモート デスクトップ サービスを実行するサーバーで UDP を使用できるようになっています。 使用できるようにするには、仮想マシン (VM) に十分なハードウェア リソースが必要です。具体的には次のものです。CPU、メモリと、最も重要であるグラフィックス処理装置 (GPU)。 (GPU は、ハイ パフォーマンス クラウド ワークステーションの最も重要なコンポーネントと言えます)。Windows Server 2016 には、基になるグラフィックス機能にアクセスするためのいくつかのオプションが用意されています。 Windows Advanced Rasterization Platform (WARP) とも呼ばれる既定の RDS GPU ソリューションは、ナレッジ ワーカーのシナリオに適したソリューションですが、提供されるリソースは、クラウド ワークステーションのシナリオには適していません。 RemoteFX vGPU は、リモート接続用に導入された、RemoteFX の機能です。サーバーあたりのユーザー密度が高いシナリオに対応し、バースト時に高い GPU 使用率を実現します。 ただし、GPU の能力を使用する場合には、GPU の能力を最大限に活用するために、Discrete Device Assignment (DDA) が必要です。

NV シリーズ VM は、Azure N シリーズ製品の一部として、1 つまたは複数の NVDIA GPU と共に提供されます。 これらの VM は、OpenGL や DirectX などのフレームワークを使用する、リモート視覚化と VDI のシナリオに最適化されています。 最大 4 つの GPU に拡張することで、Azure 上の DDA を使用して GPU を最大限に活用するワークステーションをプロビジョニングできます。

特筆すべき点は、Azure プラットフォームのプログラマビリティです。 これにより、VM のいくつかのオプションが提供されます。 たとえば、ワークステーションをオンデマンドでプロビジョニングできます。 また、Premium Storage 上の Azure ディスクまたは Azure Files を使用して、リモート マシンの状態をローカル ファイルに保持することもできます。 これらのオプションにより、コストを管理できます。 Microsoft と Citrix のパートナーシップによって提供される XenDesktop および XenApp ソリューションも、デスクトップ仮想化ソリューションのもう 1 つの選択肢となります。

## <a name="analysis-and-simulation"></a>解析とシミュレーション

コンピューター上の物理システムの解析とシミュレーションは長年行われてきました。 有限要素解析 (FEA) は、多くの解析型問題の解決に使用される数値的方法です。 FEA では、大規模な行列計算を実行するために多くの計算能力が必要になります。 2D から 3D に移行し、FEA メッシュに細分性を追加すると、FEA モデルのソリューションに含まれる行列の数が爆発的に増加します。 そのため、オンデマンドでデプロイされる処理能力が必要です。
リソースのスケーラビリティを活用するには、問題解決コードを並行実行できることが重要になります。

シミュレーションの問題を解決するには、大規模なコンピューティング リソースが必要です。 ハイ パフォーマンス コンピューティング (HPC) は、大規模なコンピューティングの一種です。 HPC では、高速並列計算に対応するリモート ダイレクト メモリ アクセス (RDMA) 機能を使用して、バックエンド ネットワークを低遅延にする必要があります。 Azure プラットフォームでは、ハイ パフォーマンス コンピューティング向けに構築された VM を提供します。 これらの VM は、DDR4 メモリと組み合わせた専用のプロセッサを備えており、Linux インストールと Windows インストールの両方で、コンピューティング集約型ソリューションを効果的に実行できます。 また、複数の VM サイズが用意されています。 「[ハイ パフォーマンス コンピューティング VM のサイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json?WT.mc_id=computeinmanufacturing-docs-ercenk)」をご覧ください。
Azure による他の方法での HPC のサポートについては、「Big Compute:[HPC & Batch](https://azure.microsoft.com/solutions/big-compute/?WT.mc_id=computeinmanufacturing-docs-ercenk)」をご覧ください。

Azure プラットフォームでは、ソリューションのスケールアップとスケールアウトが可能です。よく知られているシミュレーション用ソフトウェア パッケージの 1 つが、CD-adapco の STAR-CCM+ です。 "Le Mans 100 Million Cell" 計算流体力学 (CFD) モデルを実行する STAR-CCM+ を示す[発表された研究](https://azure.microsoft.com/blog/availability-of-star-ccm-on-microsoft-azure/?WT.mc_id=computeinmanufacturing-docs-ercenk)で、プラットフォームのスケーラビリティを垣間見ることができます。 次のグラフは、シミュレーションの実行時にコアを追加していったときの実際のスケーラビリティを示しています。

![https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/34f1873b-4db5-4c62-b963-8bdf3966cf60.png](assets/bigcompute-assets/starccm.png)

もう 1 つの一般的なエンジニアリング解析ソフトウェア パッケージは ANSYS CFD です。 エンジニアはこのパッケージを使用して、流体力、熱効果、構造的完全性、電磁放射などのマルチフィジックス解析を実行できます。 [発表された研究](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/?WT.mc_id=computeinmanufacturing-docs-ercenk)では、Azure 上でのソリューションのスケーラビリティを示しており、これも同様の結果を示しています。

![https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/77129585-f25c-4c29-b22b-80c627d03daa.png](assets/bigcompute-assets/fluent.png)

ローカル コンピューティング クラスターに投資する代わりに、並列実行が必要なソフトウェア パッケージを Azure 仮想マシンにデプロイできます。また、オールクラウド ソリューションに [HPC および GPU VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json?WT.mc_id=computeinmanufacturing-docs-ercenk) ファミリを使用して、[仮想マシン スケール セット (VMSS)](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview?WT.mc_id=computeinmanufacturing-docs-ercenk) にデプロイすることもできます。

### <a name="burst-to-azure"></a>Burst to Azure

ローカル クラスターを使用できる場合は、別のオプションとして、クラスターを Azure に拡張することによって、ピーク時のワークロードをオフロードします ("Azure へのバースト" とも呼ばれます)。 そのためには、Azure をサポートするオンプレミスのワークロード マネージャー ([Alces Flight Compute](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview?WT.mc_id=computeinmanufacturing-docs-ercenk)、[TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/?WT.mc_id=computeinmanufacturing-docs-ercenk)、[Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)、[IBM Spectrum Symphony および Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/?WT.mc_id=computeinmanufacturing-docs-ercenk)、[PBS Pro](http://pbspro.org/)、[Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx?WT.mc_id=computeinmanufacturing-docs-ercenk) など) のいずれかを使用する必要があります。

もう 1 つのオプションは Azure Batch です。これは、大規模な並列ジョブと HPC バッチ ジョブを効率的に実行するサービスです。 Azure Batch では、Message Passing Interface (MPI) API を使用するジョブを実行できます。 Batch では、[HPC](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc?WT.mc_id=computeinmanufacturing-docs-ercenk) および [GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu?WT.mc_id=computeinmanufacturing-docs-ercenk) に 最適化された VM ファミリで、[Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx?WT.mc_id=computeinmanufacturing-docs-ercenk) と Intel MPI の 両方をサポートします。 また、Microsoft は [Cycle Computing](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/?WT.mc_id=computeinmanufacturing-docs-ercenk) を買収しており、Azure でクラスターを実行するための抽象度の高いソリューションが提供されます。 [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage?WT.mc_id=computeinmanufacturing-docs-ercenk) や [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview?WT.mc_id=computeinmanufacturing-docs-ercenk) などの補完的な Azure サービスにシームレスにアクセスして、Azure で [Cray スーパーコンピューター](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure)を実行することもできます。

## <a name="generative-design"></a>ジェネレーティブ デザイン

設計プロセスは常に反復的なプロセスです。 設計者は、目標設計の一連の制約とパラメーターから始め、複数の設計代替案を反復し、最終的に制約を満たす設計を決定します。
しかし、計算能力が実質的に無限である場合、少数ではなく、"*数千*" あるいは "*数百万*" 個の設計代替案を評価できます。 この取り組みは、パラメトリック モデルと CAD ツールでのそれらのモデルの使用から始まりました。 現在、クラウド プラットフォーム上には膨大な数の計算リソースがあるため、業界は次の段階に進もうとしています。 ジェネレーティブ デザインは、パラメーターと制約をソフトウェア ツールに提供する設計プロセスを表す用語です。 その後、ツールによって設計代替案が生成され、ソリューションの複数の順列が作成されます。
ジェネレーティブ デザインには、トポロジー最適化、格子構造最適化、サーフェス最適化、形状合成などのアプローチがあります。 これらのアプローチの詳細については、この記事では取り上げません。 ただし、これらのアプローチの共通パターンは、コンピューティング集約型の環境へのアクセスの必要性です。

ジェネレーティブ デザインでは、まず、アルゴリズムが反復処理する必要がある設計パラメーター、および妥当な増分値と値の範囲を定義します。 次に、アルゴリズムによって、これらのパラメーターの有効な組み合せごとに設計代替案が作成されます。 その結果、膨大な数の設計代替案が作成されます。 これらの代替案を作成するには、多くのコンピューティング リソースが必要です。
また、設計代替案ごとに、シミュレーションおよび解析タスクをすべて実行する必要があります。 最終的に、非常に大規模なコンピューティング環境が必要になります。

[Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview?WT.mc_id=computeinmanufacturing-docs-ercenk) や [VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview?WT.mc_id=computeinmanufacturing-docs-ercenk) を使用して、コンピューティング ニーズに応じてオンデマンドでスケールアップするための Azure の複数オプションは、これらのワークロードに対応することを目的としています。

## <a name="machine-learning-ml"></a>機械学習 (ML)

非常に単純なレベルでは、ML システムは、"1 つのデータ ポイントまたは一連のデータ ポイントを指定すると、システムは相関結果を返す" というように一般化できます。 このように、ML システムは、次のような質問を解決するために使用されます。

- 過去の住宅価格と各住宅の特性を指定した場合: 市場に登場する特定の住宅の予測価格はいくらか?

- さまざまなセンサーの測定値と機械の過去の故障例を指定した場合: 次の期間にその機械が故障する可能性はどれくらいか?

- 一連の画像を指定した場合: 家猫はどれか?

- 石油パイプラインのビデオ フィードを指定した場合: かなりのへこみがある破損箇所はあるか?

人工知能 (AI) と機械学習 (ML) を使用した高度な分析機能の追加は、次のようなプロセスを使用したモデルの開発から始まります。

![](assets/bigcompute-assets/aipipeline.png)

[アルゴリズムの選択](https://docs.microsoft.com/azure/machine-learning/studio/algorithm-choice?WT.mc_id=computeinmanufacturing-docs-ercenk)は、データのサイズ、品質、性質と、想定される答えの種類に左右されます。 入力サイズ、選択したアルゴリズム、コンピューティング環境に基づき、通常、このステップには大規模なコンピューティング集約型リソースが必要であり、所要時間が異なる可能性があります。 次の表は、ML アルゴリズムのトレーニングのベンチマークに関する[技術記事](https://blogs.technet.microsoft.com/machinelearning/2017/07/25/lessons-learned-benchmarking-fast-machine-learning-algorithms/?WT.mc_id=computeinmanufacturing-docs-ercenk)に掲載されているものです。さまざまなアルゴリズム、データセット サイズ、計算対象 (GPU または CPU) を指定して、トレーニング サイクルが完了するまでの時間を示しています。

![](assets/bigcompute-assets/vmsizes.png)

決定の主要要因はビジネス上の問題です。 適切なアルゴリズムを使用して、大規模なデータセットを処理する必要がある問題の場合、重要な要素は、アルゴリズムをトレーニングするためのクラウド規模のコンピューティング リソースです。
[Azure Batch AI](https://azure.microsoft.com/services/batch-ai/?WT.mc_id=computeinmanufacturing-docs-ercenk) は、AI モデルを並列で大規模にトレーニングするサービスです。

Azure Batch AI を使用すると、データ サイエンティストは、[Azure Data Science Virtual Machine (DSVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview?WT.mc_id=computeinmanufacturing-docs-ercenk) または [Azure Deep Learning Virtual Machine (DLVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/deep-learning-dsvm-overview?WT.mc_id=computeinmanufacturing-docs-ercenk) を使用してワークステーション上のソリューションを開発し、トレーニングをクラスターにプッシュできます。 DSVM と DLVM は、プレインストールされた一連のツールとサンプルの豊富なセットを備えた、特別に構成された VM イメージです。

![](assets/bigcompute-assets/azurebatchai.png)

## <a name="conclusion"></a>まとめ

製造業界は、GPU (グラフィックス処理装置) をはじめとするハイエンドのハードウェア コンポーネントへのアクセスを使用した、膨大な数の数学的計算を必要としています。 リソースをホストするプラットフォームのスケーラビリティと弾力性が不可欠となります。 コストを管理するには、最適な速度を提供しながら、プラットフォームをオンデマンドで利用できる必要があります。

Microsoft Azure プラットフォームには、これらのニーズを満たすための幅広い選択肢が用意されています。 ゼロから始め、すべてのリソースとその側面を制御して、独自のソリューションを構築できます。 また、ソリューションの作成を迅速化する Microsoft のパートナーを見つけることもできます。 パートナーは、Azure の力を活用する方法を知っています。

## <a name="next-steps"></a>次の手順

- [NV シリーズ VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu?WT.mc_id=computeinmanufacturing-docs-ercenk) をデプロイしてクラウド ワークステーションをセットアップする

- Azure HPC の機能を活用するために、設計のニーズに対応するツールを展開する際の[オプション](https://docs.microsoft.com/azure/virtual-machines/linux/high-performance-computing?WT.mc_id=computeinmanufacturing-docs-ercenk)を確認する

- [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=computeinmanufacturing-docs-ercenk) による可能性について学ぶ
