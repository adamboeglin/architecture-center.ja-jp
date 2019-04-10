---
title: 'CAF: クラウド変革のための財務モデルを作成する'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: クラウド変革のための財務モデルを作成する方法。
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.openlocfilehash: e74a4782db5733271a575ce47cd79829f9114508
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58247983"
---
# <a name="create-a-financial-model-for-cloud-transformation"></a><span data-ttu-id="4d834-103">クラウド変革のための財務モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="4d834-103">Create a financial model for cloud transformation</span></span>

<span data-ttu-id="4d834-104">すべてのクラウド変革の完全なビジネス価値を正確に表す財務モデルの作成は複雑になることがあります。</span><span class="sxs-lookup"><span data-stu-id="4d834-104">Creating a financial model that accurately represents the full business value of any cloud transformation can be complicated.</span></span> <span data-ttu-id="4d834-105">財務モデルや業務の妥当性は、組織によって異なる傾向があります。</span><span class="sxs-lookup"><span data-stu-id="4d834-105">Financial models and business justifications tend to be different from one organization to the next.</span></span> <span data-ttu-id="4d834-106">この記事ではいくつかの数式を確立し、財務モデルを作成するときに見逃されがちないくつかの点を指摘します。</span><span class="sxs-lookup"><span data-stu-id="4d834-106">This article establishes some formulas and points out a few things that are commonly missed when creating a financial model.</span></span>

## <a name="return-on-investment-roi"></a><span data-ttu-id="4d834-107">投資収益率 (ROI)</span><span class="sxs-lookup"><span data-stu-id="4d834-107">Return on investment (ROI)</span></span>

<span data-ttu-id="4d834-108">投資収益率 (ROI) は多くの場合、経営幹部または役員会にとって重要な基準です。</span><span class="sxs-lookup"><span data-stu-id="4d834-108">Return on investment (ROI) is often an important criteria with the C-Suite or the board.</span></span> <span data-ttu-id="4d834-109">ROI は、制限された資本資源を投資する各種の方法を比較するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-109">ROI is used to compare different ways to invest limited capital resources.</span></span> <span data-ttu-id="4d834-110">ROI の数式はかなり単純です。</span><span class="sxs-lookup"><span data-stu-id="4d834-110">The formula for ROI is fairly simple.</span></span> <span data-ttu-id="4d834-111">この数式への各入力を作成するために必要な詳細は、それほど単純ではない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4d834-111">The details required to create each input to the formula may not be as simple.</span></span> <span data-ttu-id="4d834-112">基本的に、ROI は初期投資から生成された収益の金額です。</span><span class="sxs-lookup"><span data-stu-id="4d834-112">Essentially, ROI is the amount of return produced from an initial investment.</span></span> <span data-ttu-id="4d834-113">通常、これは次の割合として表されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-113">Usually it is represented as a percentage:</span></span>

![投資収益率 (ROI) = (投資利益 – 投資のコスト) / 投資のコスト](../_images/formula-roi.png)

<!-- markdownlint-disable MD036 -->
<span data-ttu-id="4d834-115">*ROI = (投資利益 &minus; 初期投資) / 初期投資*</span><span class="sxs-lookup"><span data-stu-id="4d834-115">*ROI = (Gain from Investment &minus; Initial Investment) / Initial Investment*</span></span>
<!-- markdownlint-enable MD036 -->

<span data-ttu-id="4d834-116">次のセクションでは、初期投資と投資利益 (収益) を計算するために必要なデータについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4d834-116">In the next sections, we will walk through the data needed to calculate the initial investment and the gain from investment (earnings).</span></span>

## <a name="calculating-initial-investment"></a><span data-ttu-id="4d834-117">初期投資の計算</span><span class="sxs-lookup"><span data-stu-id="4d834-117">Calculating initial investment</span></span>

<span data-ttu-id="4d834-118">初期投資は、変革を完了するために必要な設備投資 (CapEx) と営業経費 (OpEx) です。</span><span class="sxs-lookup"><span data-stu-id="4d834-118">Initial investment is the capital expenditure (CapEx) and operating expenditure (OpEx) required to complete a transformation.</span></span> <span data-ttu-id="4d834-119">コストの分類は、会計モデルや CFO の優先順位によって異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="4d834-119">The classification of costs can vary depending on accounting models and CFO preference.</span></span> <span data-ttu-id="4d834-120">ただし、このカテゴリには次のようなものが含まれます。変革するためのプロフェッショナル サービス、変革中にのみ使用されるソフトウェア ライセンス、変革中のクラウド サービスのコスト、および可能性として変革中の定額給従業員のコスト。</span><span class="sxs-lookup"><span data-stu-id="4d834-120">However, this category would include things like: Professional services to transform, software licenses that are used solely during the transformation, cost of cloud services during the transformation, and potentially the cost of the salaried employees during the transformation.</span></span>

<span data-ttu-id="4d834-121">初期投資の見積もりを作成するには、これらのコストをまとめて追加します。</span><span class="sxs-lookup"><span data-stu-id="4d834-121">Add these costs together to create an estimate of the initial investment.</span></span>

## <a name="calculating-the-gain-from-investment"></a><span data-ttu-id="4d834-122">投資利益の計算</span><span class="sxs-lookup"><span data-stu-id="4d834-122">Calculating the gain from investment</span></span>

<span data-ttu-id="4d834-123">投資利益には多くの場合、計算のための 2 番目の数式が必要です。これは、ビジネス成果とそれに関連した技術的な変更にきわめて固有のものです。</span><span class="sxs-lookup"><span data-stu-id="4d834-123">Gain from investment often requires a second formula for calculation, which is very specific to the business outcomes and associated technical changes.</span></span> <span data-ttu-id="4d834-124">収益は、コストの削減の計算ほど単純ではありません。</span><span class="sxs-lookup"><span data-stu-id="4d834-124">Earnings are not as simple as calculating reduction in costs.</span></span>

<span data-ttu-id="4d834-125">収益を計算するには、次の 2 つの変数が必要です。</span><span class="sxs-lookup"><span data-stu-id="4d834-125">To calculate earnings, two variables are required:</span></span>

![投資利益 = 収益差分 + コスト差分](../_images/formula-gain-from-investment.png)

<!-- markdownlint-disable MD036 -->
<span data-ttu-id="4d834-127">*投資利益 = 収益差分 + コスト差分*</span><span class="sxs-lookup"><span data-stu-id="4d834-127">*Gain from Investment = Revenue Deltas + Cost Deltas*</span></span>
<!-- markdownlint-enable MD036 -->

<span data-ttu-id="4d834-128">次にそれぞれについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4d834-128">Each is described below.</span></span>

## <a name="revenue-delta"></a><span data-ttu-id="4d834-129">収益差分</span><span class="sxs-lookup"><span data-stu-id="4d834-129">Revenue delta</span></span>

<span data-ttu-id="4d834-130">収益差分は、企業と協力して予測する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d834-130">Revenue delta should be forecasted in partnership with the business.</span></span> <span data-ttu-id="4d834-131">企業の利害関係者が収益への影響について同意したら、それを使用して収益状況を改善することができます。</span><span class="sxs-lookup"><span data-stu-id="4d834-131">Once the business stakeholders agree on a revenue impact, that can be used to improve the earning position.</span></span>

## <a name="cost-deltas"></a><span data-ttu-id="4d834-132">コスト差分</span><span class="sxs-lookup"><span data-stu-id="4d834-132">Cost deltas</span></span>

<span data-ttu-id="4d834-133">コスト差分は、変革の結果として発生する増加または減少の金額です。</span><span class="sxs-lookup"><span data-stu-id="4d834-133">Cost deltas are the amount of increase or decrease that will come as a result of the transformation.</span></span> <span data-ttu-id="4d834-134">コスト差分に影響を与える場合がある独立変数は多数存在します。</span><span class="sxs-lookup"><span data-stu-id="4d834-134">There are a number of independent variables that can affect cost deltas.</span></span> <span data-ttu-id="4d834-135">収益は主に、資本コストの削減、コストの回避、運用コストの削減、減価償却の削減などのハード コストに基づいています。</span><span class="sxs-lookup"><span data-stu-id="4d834-135">Earnings are largely based on hard costs like capital expense reductions, cost avoidance, operational cost reductions, and depreciation reductions.</span></span> <span data-ttu-id="4d834-136">以降のセクションは、考慮すべきコスト差分の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="4d834-136">The following sections are examples of cost deltas to consider.</span></span>

### <a name="depreciation-reductions-or-acceleration"></a><span data-ttu-id="4d834-137">減価償却の削減または促進</span><span class="sxs-lookup"><span data-stu-id="4d834-137">Depreciation reductions or acceleration</span></span>

<span data-ttu-id="4d834-138">減価償却に関するガイダンスについては、CFO または財務チームに問い合わせてください。</span><span class="sxs-lookup"><span data-stu-id="4d834-138">For guidance on depreciation, speak with the CFO or finance team.</span></span> <span data-ttu-id="4d834-139">次の説明は、減価償却のトピックに関する一般的なリファレンスとして機能するように考慮されています。</span><span class="sxs-lookup"><span data-stu-id="4d834-139">The following is meant to serve as a general reference on the topic of depreciation.</span></span>

<span data-ttu-id="4d834-140">資産の取得に資本が投資されたら、予測される資産の有効期間を通して継続的なメリットを生み出すために、その投資を財務または税金の目的に使用できます。</span><span class="sxs-lookup"><span data-stu-id="4d834-140">When capital is invested in the acquisition of an asset, that investment could be used for financial or tax purposes to produce ongoing benefits over the expected lifespan of the asset.</span></span> <span data-ttu-id="4d834-141">一部の企業は、減価償却を積極的な税金のメリットと見なしています。</span><span class="sxs-lookup"><span data-stu-id="4d834-141">Some companies see depreciation as a positive tax advantage.</span></span> <span data-ttu-id="4d834-142">その他の企業は、年間 IT 予算に寄与する他の経常経費と同様に、それをコミットされた継続的な経費と見なしています。</span><span class="sxs-lookup"><span data-stu-id="4d834-142">Others see it as committed, ongoing expense similar to other recurring expenses attributed to the annual IT budget.</span></span>

<span data-ttu-id="4d834-143">減価償却の除外が可能かどうか、およびそれがコスト差分への積極的な貢献になるかどうかを確認するには、財務オフィスに問い合わせてください。</span><span class="sxs-lookup"><span data-stu-id="4d834-143">Speak with the finance office to see if elimination of depreciation is possible, and if it would make a positive contribution to cost deltas.</span></span>

### <a name="physical-asset-recovery"></a><span data-ttu-id="4d834-144">物理的な資産の復旧</span><span class="sxs-lookup"><span data-stu-id="4d834-144">Physical asset recovery</span></span>

<span data-ttu-id="4d834-145">場合によっては、廃棄された資産を収益源として販売できます。</span><span class="sxs-lookup"><span data-stu-id="4d834-145">In some cases, retired assets can be sold as a source of revenue.</span></span> <span data-ttu-id="4d834-146">多くの場合、この収益は、簡単にするためにコスト削減にまとめられます。</span><span class="sxs-lookup"><span data-stu-id="4d834-146">Often, this revenue is lumped into cost reduction for simplicity.</span></span> <span data-ttu-id="4d834-147">ただし、それは実際には収益の増加であるため、課税される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4d834-147">However, it's truly an increase in revenue and may be taxed as such.</span></span> <span data-ttu-id="4d834-148">このオプションの使用可能性、および結果として得られる収益について説明する方法を理解するには、財務オフィスに問い合わせてください。</span><span class="sxs-lookup"><span data-stu-id="4d834-148">Speak with the finance office to understand the viability of this option and how to account for the resulting revenue.</span></span>

### <a name="operational-cost-reductions"></a><span data-ttu-id="4d834-149">運用コストの削減</span><span class="sxs-lookup"><span data-stu-id="4d834-149">Operational cost reductions</span></span>

<span data-ttu-id="4d834-150">事業を運営するために必要な経常経費は多くの場合、営業経費 (OpEx) として参照されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-150">Recurring expenses required to operate the business are often referred to as operational expenses (OpEx).</span></span> <span data-ttu-id="4d834-151">OpEx は、非常に幅広いカテゴリです。</span><span class="sxs-lookup"><span data-stu-id="4d834-151">OpEx is a very broad category.</span></span> <span data-ttu-id="4d834-152">ほとんどの会計モデルでは、これにはソフトウェア ライセンス、ホスティング経費、電気代、不動産賃貸料、冷却経費、稼働に必要な一時的なスタッフ、装置レンタル料、交換部品、メンテナンス契約、修理サービス、ビジネス継続性/ディザスター リカバリー (BC/DR) サービス、および資本コストの承認を必要としないその他のいくつかの経費が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4d834-152">In most accounting models, it would include software licensing, hosting expenses, electric bills, real estate rentals, cooling expenses, temporary staff required for operations, equipment rentals, replacement parts, maintenance contracts, repair services, Business Continuity/Disaster Recovery (BC/DR) services, and a number of other expenses that don't require capital expense approvals.</span></span>

<span data-ttu-id="4d834-153">このカテゴリは、事業変革を考慮したときの最大の収益領域の 1 つです。</span><span class="sxs-lookup"><span data-stu-id="4d834-153">This category is one of the largest earnings areas when considering an Operational Transformation.</span></span> <span data-ttu-id="4d834-154">この一覧を網羅的にするために投資された時間は、めったに浪費されません。</span><span class="sxs-lookup"><span data-stu-id="4d834-154">Time invested in making this list exhaustive is seldom wasted.</span></span> <span data-ttu-id="4d834-155">すべての運用コストの理由を説明できることを確認するには、CIO および財務チームに問い合わせてください。</span><span class="sxs-lookup"><span data-stu-id="4d834-155">Ask questions of the CIO and finance team to ensure all operational costs are accounted for.</span></span>

### <a name="cost-avoidance"></a><span data-ttu-id="4d834-156">コストの回避</span><span class="sxs-lookup"><span data-stu-id="4d834-156">Cost avoidance</span></span>

<span data-ttu-id="4d834-157">営業経費 (OpEx) が予測されるが、まだ承認済みの予算にない場合、それはコスト削減のカテゴリに適合しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4d834-157">When an operational expense (OpEx) is expected, but not yet in an approved budget, it may not fit into a cost reduction category.</span></span> <span data-ttu-id="4d834-158">たとえば、VMWare と Microsoft のライセンスを再調整して翌年に支払う必要がある場合、それらはまだ完全に適格なコストではありません。</span><span class="sxs-lookup"><span data-stu-id="4d834-158">For instance, if VMWare and Microsoft licenses need to be renegotiated and paid next year, they aren't fully qualified costs yet.</span></span> <span data-ttu-id="4d834-159">これらの予測されるコストの削減は、コスト差分の計算のために運用コストとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-159">Reductions in those expected costs would be treated like operational costs for the sake of cost delta calculations.</span></span> <span data-ttu-id="4d834-160">ただし非公式には、調整および予算の承認が完了するまで、これらは "コストの回避" として参照されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-160">Informally, however, they should be referred to as "cost avoidance," until negotiation and budget approval is complete.</span></span>

### <a name="soft-cost-reductions"></a><span data-ttu-id="4d834-161">ソフト コストの削減</span><span class="sxs-lookup"><span data-stu-id="4d834-161">Soft cost reductions</span></span>

<span data-ttu-id="4d834-162">一部の企業では、運用の複雑さの削減や、データセンターを運営するフルタイム スタッフの削減などのソフト コストも含めることができます。</span><span class="sxs-lookup"><span data-stu-id="4d834-162">In some companies, soft costs such as reductions in operational complexity or reduction in full-time staff to operate a datacenter could also be included.</span></span> <span data-ttu-id="4d834-163">ただし、ソフト コストを含めることはお勧めできない場合があります。</span><span class="sxs-lookup"><span data-stu-id="4d834-163">However, including soft costs can be ill-advised.</span></span> <span data-ttu-id="4d834-164">ソフト コストを含めると、コストの削減が明白なコスト節約になるという、ドキュメントに記載されていない前提が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-164">Including soft costs inserts an undocumented assumption that the reduction in costs will equate to tangible cost savings.</span></span> <span data-ttu-id="4d834-165">テクノロジ プロジェクトは、めったにソフト コストの実際の復旧につながりません。</span><span class="sxs-lookup"><span data-stu-id="4d834-165">Technology projects seldom result in actual soft cost recovery.</span></span>

### <a name="headcount-reductions"></a><span data-ttu-id="4d834-166">社員数の削減</span><span class="sxs-lookup"><span data-stu-id="4d834-166">Headcount reductions</span></span>

<span data-ttu-id="4d834-167">スタッフの時間短縮は多くの場合、ソフト コストの削減に含まれます。</span><span class="sxs-lookup"><span data-stu-id="4d834-167">Time savings for staff are often included under soft cost reduction.</span></span> <span data-ttu-id="4d834-168">これらの時間短縮が実際の IT 給与またはスタッフの削減にマッピングされると、それは社員数の削減として個別に計算できます。</span><span class="sxs-lookup"><span data-stu-id="4d834-168">When those time savings map to actual reduction of IT salary or staffing, it could be calculated separately as a headcount reduction.</span></span>

<span data-ttu-id="4d834-169">とは言え、オンプレミスで必要なスキルは一般に、クラウドで必要な同様の (またはより高いレベルの) スキル セットにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="4d834-169">That said, the skills needed on-premises generally map to a similar (or higher level) set of skills needed in the cloud.</span></span> <span data-ttu-id="4d834-170">つまり、人びとは一般に、クラウド移行の後は解雇されません。</span><span class="sxs-lookup"><span data-stu-id="4d834-170">That means people generally don't get laid off after a cloud migration.</span></span>

<span data-ttu-id="4d834-171">1 つの例外は、運用容量がサード パーティまたは管理サービス プロバイダー (MSP) によって提供される場合です。</span><span class="sxs-lookup"><span data-stu-id="4d834-171">An exception is when operational capacity is provided by a third party or managed services provider (MSP).</span></span> <span data-ttu-id="4d834-172">IT システムがサード パーティによって管理される場合は、運用コストをクラウド ネイティブなソリューションまたはクラウド ネイティブな MSP で置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="4d834-172">If IT systems are managed by a third party, the costs to operate could be replaced by a cloud-native solution or cloud-native MSP.</span></span> <span data-ttu-id="4d834-173">クラウド ネイティブな MSP はより低いコストで、より効率的に運用する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4d834-173">A cloud native MSP is likely to operate more efficiently and potentially at a lower cost.</span></span> <span data-ttu-id="4d834-174">その場合、運用コストの削減はハード コストの計算に属します。</span><span class="sxs-lookup"><span data-stu-id="4d834-174">If that's the case, operational cost reductions belong in the hard cost calculations.</span></span>

### <a name="capital-expense-reductions-or-avoidance"></a><span data-ttu-id="4d834-175">資本コストの削減または回避</span><span class="sxs-lookup"><span data-stu-id="4d834-175">Capital expense reductions or avoidance</span></span>

<span data-ttu-id="4d834-176">資本コスト (CapEx) は、営業経費とは少し異なります。</span><span class="sxs-lookup"><span data-stu-id="4d834-176">Capital expenses (CapEx) are slightly different that operational expenses.</span></span> <span data-ttu-id="4d834-177">一般に、このカテゴリは更新サイクルまたはデータセンターの拡張によって促進されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-177">Generally, this category is driven by refresh cycles or datacenter expansion.</span></span> <span data-ttu-id="4d834-178">データセンターの拡張の例には、ビッグ データ ソリューションまたはデータ ウェアハウスをホストする新しい高パフォーマンスのクラスターがあり、一般に CapEx カテゴリに適合します。</span><span class="sxs-lookup"><span data-stu-id="4d834-178">An example of a datacenter expansion would be a new high-performance cluster to host a Big Data solution or data warehouse, and would generally fit into a CapEx category.</span></span> <span data-ttu-id="4d834-179">より一般的なのは、基本的な更新サイクルです。</span><span class="sxs-lookup"><span data-stu-id="4d834-179">More common are the basic refresh cycles.</span></span> <span data-ttu-id="4d834-180">一部の企業には、厳格なハードウェア更新サイクルがあります。つまり、資産は定期的なサイクルで (通常は 3、5、または 8 年ごとに) 廃棄および交換されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-180">Some companies have rigid hardware refresh cycles, meaning assets are retired and replaced on a regular cycle (usually every three, five, or eight years).</span></span> <span data-ttu-id="4d834-181">これらのサイクルは多くの場合、資産のリース サイクルまたは予測される装置の有効期間と一致します。</span><span class="sxs-lookup"><span data-stu-id="4d834-181">These cycles often coincide with asset lease cycles or forecasted lifespan of equipment.</span></span> <span data-ttu-id="4d834-182">更新サイクルに達した場合、IT は新しい装置を取得するために CapEx を取得します。</span><span class="sxs-lookup"><span data-stu-id="4d834-182">When a refresh cycle hits, IT draws CapEx to acquire new equipment.</span></span>

<span data-ttu-id="4d834-183">更新サイクルが承認され、予算化されている場合は、クラウド変革がそのコストの解消に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="4d834-183">If a refresh cycle is approved and budgeted, the Cloud Transformation could help eliminate that cost.</span></span> <span data-ttu-id="4d834-184">更新サイクルが計画されているが、まだ承認されていない場合は、クラウド変革によって CapEx コストの回避が作成されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-184">If a refresh cycle is planned but not yet approved, the Cloud Transformation could create a CapEx cost avoidance.</span></span> <span data-ttu-id="4d834-185">どちらのシナリオもコスト差分に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4d834-185">Both scenarios would be added to the cost delta.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4d834-186">次の手順</span><span class="sxs-lookup"><span data-stu-id="4d834-186">Next steps</span></span>

<span data-ttu-id="4d834-187">クラウド変革のコンテキストにおける財務上の結果の例を確認します。</span><span class="sxs-lookup"><span data-stu-id="4d834-187">Read some example fiscal outcomes in the context of a cloud transformation.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="4d834-188">財務上の結果の例</span><span class="sxs-lookup"><span data-stu-id="4d834-188">Examples of fiscal outcomes</span></span>](./business-outcomes/fiscal-outcomes.md)