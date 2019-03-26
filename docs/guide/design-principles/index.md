---
title: Azure アプリケーションの設計原則
titleSuffix: Azure Application Architecture Guide
description: Azure アプリケーションの設計原則を示します。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
---

# <a name="ten-design-principles-for-azure-applications"></a><span data-ttu-id="c884e-103">Azure アプリケーションの 10 の設計原則</span><span class="sxs-lookup"><span data-stu-id="c884e-103">Ten design principles for Azure applications</span></span>

<span data-ttu-id="c884e-104">次の設計原則に従って、アプリケーションのスケーラビリティを上げて、回復力や管理しやすさを強化します。</span><span class="sxs-lookup"><span data-stu-id="c884e-104">Follow these design principles to make your application more scalable, resilient, and manageable.</span></span>

<span data-ttu-id="c884e-105">**[自動修復機能を設計します](self-healing.md)**。</span><span class="sxs-lookup"><span data-stu-id="c884e-105">**[Design for self healing](self-healing.md)**.</span></span> <span data-ttu-id="c884e-106">分散システムでは障害が発生します。</span><span class="sxs-lookup"><span data-stu-id="c884e-106">In a distributed system, failures happen.</span></span> <span data-ttu-id="c884e-107">障害の発生に備えてアプリケーションの自動修復機能を設計します。</span><span class="sxs-lookup"><span data-stu-id="c884e-107">Design your application to be self healing when failures occur.</span></span>

<span data-ttu-id="c884e-108">**[すべての要素を冗長にします](redundancy.md)**。</span><span class="sxs-lookup"><span data-stu-id="c884e-108">**[Make all things redundant](redundancy.md)**.</span></span> <span data-ttu-id="c884e-109">単一障害点をなくすようにアプリケーションに冗長性を組み込みます。</span><span class="sxs-lookup"><span data-stu-id="c884e-109">Build redundancy into your application, to avoid having single points of failure.</span></span>

<span data-ttu-id="c884e-110">**[調整を最小限に抑えます](minimize-coordination.md)**。</span><span class="sxs-lookup"><span data-stu-id="c884e-110">**[Minimize coordination](minimize-coordination.md)**.</span></span> <span data-ttu-id="c884e-111">アプリケーション サービス間の調整を最小限に抑えてスケーラビリティを実現します。</span><span class="sxs-lookup"><span data-stu-id="c884e-111">Minimize coordination between application services to achieve scalability.</span></span>

<span data-ttu-id="c884e-112">**[スケール アウトするように設計します](scale-out.md)**。需要に応じて新規インスタンスを追加または削除して水平方向に拡張できるようにアプリケーションを設計します。</span><span class="sxs-lookup"><span data-stu-id="c884e-112">**[Design to scale out](scale-out.md)**. Design your application so that it can scale horizontally, adding or removing new instances as demand requires.</span></span>

<span data-ttu-id="c884e-113">**[制限に対処するようにパーティション化します](partition.md)**。</span><span class="sxs-lookup"><span data-stu-id="c884e-113">**[Partition around limits](partition.md)**.</span></span> <span data-ttu-id="c884e-114">パーティション分割を使用して、データベース、ネットワーク、コンピューティングの制限に対処します。</span><span class="sxs-lookup"><span data-stu-id="c884e-114">Use partitioning to work around database, network, and compute limits.</span></span>

<span data-ttu-id="c884e-115">**[操作に合わせて設計します](design-for-operations.md)**。</span><span class="sxs-lookup"><span data-stu-id="c884e-115">**[Design for operations](design-for-operations.md)**.</span></span> <span data-ttu-id="c884e-116">運用チームが必要なツールを得られるようにアプリケーションを設計します。</span><span class="sxs-lookup"><span data-stu-id="c884e-116">Design your application so that the operations team has the tools they need.</span></span>

<span data-ttu-id="c884e-117">**[管理対象サービスを使用します](managed-services.md)**。</span><span class="sxs-lookup"><span data-stu-id="c884e-117">**[Use managed services](managed-services.md)**.</span></span> <span data-ttu-id="c884e-118">可能であればサービスとしてのインフラストラクチャ (IaaS) ではなくサービスとしてのプラットフォーム (PaaS) を使用します。</span><span class="sxs-lookup"><span data-stu-id="c884e-118">When possible, use platform as a service (PaaS) rather than infrastructure as a service (IaaS).</span></span>

<span data-ttu-id="c884e-119">**[ジョブに最適なデータ ストアを使用します](use-the-best-data-store.md)**。</span><span class="sxs-lookup"><span data-stu-id="c884e-119">**[Use the best data store for the job](use-the-best-data-store.md)**.</span></span> <span data-ttu-id="c884e-120">データに最適なストレージ技術とその使用方法を選択します。</span><span class="sxs-lookup"><span data-stu-id="c884e-120">Pick the storage technology that is the best fit for your data and how it will be used.</span></span>

<span data-ttu-id="c884e-121">**[展開を見込んで設計します](design-for-evolution.md)**。</span><span class="sxs-lookup"><span data-stu-id="c884e-121">**[Design for evolution](design-for-evolution.md)**.</span></span> <span data-ttu-id="c884e-122">成功するすべてのアプリケーションは時間の経過と共に変化します。</span><span class="sxs-lookup"><span data-stu-id="c884e-122">All successful applications change over time.</span></span> <span data-ttu-id="c884e-123">展開を見込んだ設計は継続的な技術革新のキーです。</span><span class="sxs-lookup"><span data-stu-id="c884e-123">An evolutionary design is key for continuous innovation.</span></span>

<span data-ttu-id="c884e-124">**[ビジネスのニーズに合わせて構築します](build-for-business.md)**。</span><span class="sxs-lookup"><span data-stu-id="c884e-124">**[Build for the needs of business](build-for-business.md)**.</span></span> <span data-ttu-id="c884e-125">設計の決定はすべてビジネス要件によって正当化される必要があります。</span><span class="sxs-lookup"><span data-stu-id="c884e-125">Every design decision must be justified by a business requirement.</span></span>
