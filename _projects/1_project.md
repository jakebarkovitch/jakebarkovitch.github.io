---
layout: page
title: Ascension Healthcare Projects
description: My two main projects during my data analyst internship at The Resource Group.
img: assets/img/trg_logo.jfif
importance: 1
category: professional
---

My first project was on tracking freight data for my local hospital branch that I was working out of. The hospital wanted to understand the discrepancies within their freight costs and what factors led to costlier freight. There were many issues involved since a lot of data was simply not provided by the distributors. A large portion of the project involved meeting with these vendors to request relevant data extracts and information. Eventually I was able to put together a geospatial Tableau dashboard of all the distributor warehouses with distance, quantity, freight, shipping, and description metrics. This allowed the hospital to filter for specific items such as solutions which were a target item for expensive freight. They could also analyze stat orders and where they were coming from. This project implemented many requests of the local freight team and I was able to efficiently implement them in a way that benefited them the most. I definitely learned a lot and was able to leverage them in my company wide project which I detail below.


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/Capture55.PNG" title="geospatial dashboard" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Geospatial Tableau dashboard of 60+ distributor warehouses highlighting costly freight and re-routed lines.
</div>

My second and most prevalent project involved working with the finance group purchasing organization to calculate untracked administration fees. These admin fees are related to vendor contracts where they need to pay a percentage of all spend on that contract, usually between 3-5%. The issue is that the fees are self reported by the vendors so it falls onto the finance team to track if any vendors are not paying the fees in full. This is usually easy enough for most contracts, but there are many edge cases that are hard to keep track of. One such case involves off-contract spend that is still susceptible to admin fees under specific clauses of a vendor's contract. My team's project was to create a process for tracking these fees. We led the entire design and implementation with only minor guidance from the finance team. I led the SQL development and most of the Tableau deployment. This required me to learn a lot of new skills because I did not know how to use Tableau and I only had minor SQL experience. Overall this was a very rewarding project that ended with a very beneficial product for the company.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/trg_dash.png" title="admin audit fee dashboard" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/trg_results.jpg" title="raw data" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Admin audit fee dashboard and related raw data extract from the SQL servers. Account and contract names censored for confidentiality.
</div>

Below is the SQL code I helped write to connect SQL databases and calculate relevant fees for fiscal year 2021. It spans three separate databases to join required details and filter for desired results. This SQL code fed directly into the Tableau dashboard shown above. The dashboard also had a separate connection to a second SQL server to acquire corresponding self reported fees from the vendors.

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
