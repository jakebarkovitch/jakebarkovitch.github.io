---
layout: page
title: project 1
description: My projects during my internship at The Resource Group
img: assets/img/trg_logo.jpg
importance: 1
category: professional
---

Every project has a beautiful feature showcase page.
It's easy to include images in a flexible 3-column grid format.
Make your photos 1/3, 2/3, or full width.

To give your project a background in the portfolio page, just add the img tag to the front matter like so:

    ---
    layout: page
    title: project
    description: a project with a background image
    img: /assets/img/12.jpg
    ---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Caption photos easily. On the left, a road goes through a tunnel. Middle, leaves artistically fall in a hipster photoshoot. Right, in another hipster photoshoot, a lumberjack grasps a handful of pine needles.
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/Capture55.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    This image can also have a caption. It's like magic.
</div>

You can also put regular text between your rows of images.
Say you wanted to write a little bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, *bled* for your project, and then... you reveal its glory in the next row of images.


<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/Capture5555.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/trg_results.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>


The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}
```sql
(SELECT vw_Pilot_Contracts.MainContract as contract_no,vw_Pilot_Contracts.GPO_Percent AS gpo_percent, vw_Pilot_Contracts.Discipline,
        'NON Contracted' AS spend_contract, vw_SpendData.YearMonth,
        SUM(vw_SpendData.TotalPurchase) AS spend_purchase,
    SUM((vw_Pilot_Contracts.GPO_Percent/100)*vw_SpendData.TotalPurchase) AS spend_due

--     SUM(vw_SpendData.TotalPurchase*vw_Pilot_Contracts.GPO_Percent) AS spend_due
FROM vw_SpendData
INNER JOIN vw_PriceExport_History ON vw_SpendData.MfgCatalogCode = vw_PriceExport_History.Cleansed_PartNo
AND vw_SpendData.GlobalSupplierName = vw_PriceExport_History.Cleansed_MFGName
INNER JOIN vw_Pilot_Contracts ON vw_PriceExport_History.Contract = vw_Pilot_Contracts.Contract
WHERE vw_SpendData.ContractNumber = 'NON Contracted'
    AND vw_SpendData.YearMonth >= 202007
  AND vw_SpendData.YearMonth <= 202106
    AND vw_SpendData.TotalPurchase IS NOT NULL
    AND vw_SpendData.MfgCatalogCode != 'MFGCATUNKNOWN' AND vw_SpendData.MfgCatalogCode != 'SUPPLIERCATUNKNOWN'
AND vw_PriceExport_History.Pricing_EffectiveDate <= vw_SpendData.FullDate AND vw_SpendData.FullDate <= vw_PriceExport_History.Contract_ExpirationDate
group by vw_Pilot_Contracts.MainContract, vw_Pilot_Contracts.GPO_Percent, vw_SpendData.YearMonth, vw_Pilot_Contracts.Discipline)
UNION
(SELECT vw_Pilot_Contracts.MainContract as contract_no,vw_Pilot_Contracts.GPO_Percent AS gpo_percent, vw_Pilot_Contracts.Discipline,
        vw_Pilot_Contracts.MainContract AS spend_contract,vw_SpendData.YearMonth,
        SUM(vw_SpendData.TotalPurchase) AS spend_purchase,
    SUM((vw_Pilot_Contracts.GPO_Percent/100)*vw_SpendData.TotalPurchase) AS spend_due
--     SUM(vw_SpendData.TotalPurchase*vw_Pilot_Contracts.GPO_Percent) AS total_due
FROM vw_SpendData
INNER JOIN vw_Pilot_Contracts ON vw_SpendData.ContractNumber = vw_Pilot_Contracts.Contract
WHERE vw_SpendData.ContractNumber != 'NON Contracted'
  AND vw_SpendData.YearMonth >= 202007
  AND vw_SpendData.YearMonth <= 202106
    AND vw_SpendData.TotalPurchase IS NOT NULL
group by vw_Pilot_Contracts.MainContract, vw_Pilot_Contracts.GPO_Percent,vw_SpendData.YearMonth, vw_Pilot_Contracts.Discipline)
```
{% endraw %}
