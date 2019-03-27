---
title: コンピューター支援エンジニアリング サービス
titleSuffix: Azure Example Scenarios
description: Azure で、コンピューター支援エンジニアリング (CAE) に、サービスとしてのソフトウェア (SaaS) プラットフォームを提供します。
author: alexbuckgit
ms.date: 08/22/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: HPC
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-hpc-saas.png
ms.openlocfilehash: bd38bd0042fceeab6efe04d7b6d1477d17ada7f5
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249637"
---
# <a name="a-computer-aided-engineering-service-on-azure"></a>Azure でのコンピューター支援エンジニアリング サービス

このシナリオ例では、Azure のハイ パフォーマンス コンピューティング (HPC) 機能を基に構築されたサービスとしてのソフトウェア (SaaS) プラットフォームの提供について示します。 このシナリオは、エンジニアリング ソフトウェア ソリューションが基になっています。 ただし、アーキテクチャは、画像レンダリング、複雑なモデリング、財務リスク計算など、HPC リソースを必要とする他の業界にも関連します。

この例で示すのは、コンピューター支援エンジニアリング (CAE) アプリケーションをエンジニアリング会社や製造企業に提供するエンジニアリング ソフトウェア プロバイダーです。 CAE ソリューションにより、革新が可能になり、開発時間が短縮され、製品設計の全期間を通してコストが削減されます。 これらのソリューションでは、膨大な計算リソースが必要になり、多くの場合、大量のデータが処理されます。 高コストのオンプレミス HPC アプライアンスまたはハイエンド ワークステーションのため、小さいエンジニアリング会社や起業家や学生では、これらのテクノロジを利用できないことがよくあります。

このシナリオの会社は、クラウド ベースの HPC テクノロジを利用する SaaS プラットフォームを構築することにより、アプリケーションの市場を広げようと考えています。 顧客は、必要な計算リソースに対してだけ料金を支払い、他の手段では負担しきれない膨大なコンピューティング能力にアクセスできる必要があります。

会社の目標は次のとおりです。

- Azure の HPC 機能を利用して、製品設計とテストのプロセスを高速化する。
- ハードウェアの最新の革新を使用して複雑なシミュレーションを実行する一方で、単純なシミュレーションのコストは最小限に抑える。
- ハイエンドのエンジニアリング ワークステーションを必要とせずに、Web ブラウザーで真に迫った視覚化とレンダリングを可能にする。

## <a name="relevant-use-cases"></a>関連するユース ケース

その他の関連するユース ケース:

- ゲノミクス研究
- 天気シミュレーション
- 計算化学アプリケーション

## <a name="architecture"></a>アーキテクチャ

![HPC 機能を可能にする SaaS ソリューションのアーキテクチャ][architecture]

- ユーザーは、[Apache Guacamole サービス](https://guacamole.apache.org/)を使用する HTML5 ベースの RDP 接続を備えたブラウザーを使用して、NV シリーズの仮想マシン (VM) にアクセスできます。 これらの VM インスタンスでは、レンダリングとコラボレーション タスクのための強力な GPU が提供されます。 ユーザーは、ハイエンドのモバイル コンピューティング デバイスまたはラップトップにアクセスしなくても、設計を編集し、その結果を表示できます。 スケジューラにより、ユーザー定義のヒューリスティックに基づいて追加の VM が起動されます。
- デスクトップの CAD セッションから、ユーザーはワークロードを送信して使用可能な HPC クラスター ノードで実行できます。 これらのワークロードで応力解析や計算流体力学の計算などのタスクが実行されるので、オンプレミスに専用のコンピューティング クラスターを用意する必要がなくなります。 これらのクラスター ノードは、計算リソースに対するアクティブなユーザーの要求に基づく負荷やキューの深さに応じて自動スケーリングするように構成できます。
- エンド ユーザーが使用できる Web リソースをホストするには、Azure Kubernetes Service (AKS) が使用されます。

### <a name="components"></a>コンポーネント

- [H シリーズ仮想マシン](/azure/virtual-machines/linux/sizes-hpc)は、分子モデリングや計算流体力学など、計算の多いシミュレーションの実行に使用されます。 また、ソリューションでは、リモート ダイレクト メモリ アクセス (RDMA) 接続や InfiniBand ネットワークなどのテクノロジも利用されます。
- [NV シリーズ仮想マシン](/azure/virtual-machines/windows/sizes-gpu)では、標準的な Web ブラウザーからエンジニアに対してハイエンド ワークステーションの機能が提供されます。 これらの仮想マシンには、高度なレンダリングをサポートし、単精度のワークロードを実行できる、NVIDIA Tesla M60 GPU が装備されています。
- CentOS を実行する[汎用仮想マシン](/azure/virtual-machines/linux/sizes-general)では、Web アプリケーションなどの従来型のワークロードが処理されます。
- [Application Gateway](/azure/application-gateway/overview) では、Web サーバーに届いた要求が負荷分散されます。
- [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) は、HPC または GPU 仮想マシンのハイエンド機能を必要としないシミュレーションのためのスケーラブルなワークロードを低コストで実行するために使用されます。
- [Altair PBS Works Suite](https://www.pbsworks.com/PBSProduct.aspx?n=PBS-Works-Suite&c=Overview-and-Capabilities) では HPC のワークフローが調整されて、現在の負荷を処理するのに十分な仮想マシン インスタンスを使用できることが保証されます。 また、需要が低下したときはコストを削減するために仮想マシンの割り当てが解除されます。
- [BLOB ストレージ](/azure/storage/blobs/storage-blobs-introduction)には、スケジュールされたジョブをサポートするファイルが格納されます。

### <a name="alternatives"></a>代替手段

- [Azure CycleCloud](/azure/cyclecloud/overview) を使用すると、HPC クラスターの作成、管理、運用、最適化が簡単になります。 高度なポリシーとガバナンスの機能が提供されます。 CycleCloud では、任意のジョブ スケジューラまたはソフトウェア スタックがサポートされます。
- [HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options) では、Windows Server ベースのワークロード用の Azure HPC クラスターを作成して管理できます。 Linux ベースのワークロードには HPC Pack を使用できません。
- [Azure Automation State Configuration](/azure/automation/automation-dsc-overview) では、展開する仮想マシンとソフトウェアの定義に対して、コードとしてのインフラストラクチャのアプローチが提供されます。 ジョブ キューに送信されたジョブの数に基づく計算ノードの自動スケーリング ルールを使用し、仮想マシン スケール セットの一部として仮想マシンを展開できます。 新しい仮想マシンが必要なときは、Azure イメージ ギャラリーから最新のパッチが適用されたイメージを使用して仮想マシンがプロビジョニングされた後、必要なソフトウェアがインストールされ、PowerShell DSC 構成スクリプトを使用して構成されます。
- [Azure Functions](/azure/azure-functions/functions-overview)

## <a name="considerations"></a>考慮事項

- コードとしてのインフラストラクチャのアプローチは仮想マシンのビルド定義を管理する優れた方法ですが、スクリプトを使用して新しい仮想マシンをプロビジョニングするには長い時間がかかることがあります。 このソリューションでは、DSC スクリプトを使用してゴールデン イメージを定期的に作成することで、適当な妥協点が見つかりました。このイメージを使用すると、DSC を使用し、需要に応じて仮想マシンを完全に構築より速く、新しい仮想マシンをプロビジョニングできます。 Azure DevOps Services または他の CI/CD ツールを使用すると、DSC スクリプトを使用してゴールデン イメージを定期的に更新できます。
- ソリューションの全体的なコストと、計算リソースの高速の可用性のバランスを取ることは、重要な考慮事項です。 N シリーズ仮想マシン インスタンスのプールをプロビジョニングし、それを割り当て解除された状態にすると、運用コストが減ります。 仮想マシンを追加する必要があるときの、既存インスタンスの再割り当てには、別のホストでの仮想マシンの起動が伴いますが、仮想マシンをプロビジョニング解除してから再プロビジョニングすると再起動時に GPU に対して同じ PCI バスが保持されるので、OS が GPU 用のドライバーを識別してインストールするために必要な PCI バス検出時間が除去されます。
- 元のアーキテクチャでは、シミュレーションの実行を Azure 仮想マシンに完全に依存していました。 仮想マシンのすべての機能を必要としないワークロードのコストを削減するため、これらのワークロードはコンテナー化されて、Azure Kubernetes Service (AKS) に展開されました。
- 会社の従業員は、オープン ソース テクノロジについてのスキルを既に備えていました。 Linux や Kubernetes などのテクノロジを基にすることで、これらのスキルを活用できます。

## <a name="pricing"></a>価格

このシナリオの実行コストを調べることができるよう、必要なサービスの多くは、[料金計算ツール][calculator]で事前構成されています。 ソリューションのコストは、要件を満たすために必要なサービスの数とスケールに依存します。

以下の考慮事項によって、このソリューションのコストのかなりの部分が決まります。

- Azure の仮想マシンのコストは、インスタンスが追加プロビジョニングされのに比例して増加します。 割り当てを解除された仮想マシンについては、ストレージ コストのみが発生し、コンピューティング コストはかかりません。 割り当てを解除されたマシンは、需要が増えたら再割り当てできます。
- Azure Kubernetes Service のコストは、ワークロードをサポートするために選択された VM の種類に基づきます。 コストは、クラスター内の VM の数に比例して増加します。

## <a name="next-steps"></a>次の手順

- [Altair 社の導入事例][source-document]を読んでください。 このシナリオ例は、そのアーキテクチャのバージョンが基になっています。
- Azure で利用できる他の[ビッグ コンピューティング ソリューション](https://azure.microsoft.com/solutions/big-compute)を確認してください。

<!-- links -->
[architecture]: ./media/architecture-hpc-saas.png
[source-document]: https://customers.microsoft.com/story/altair-manufacturing-azure
[calculator]: https://azure.com/e/3cb9ccdc893f41ffbcdb00c328178ccf
