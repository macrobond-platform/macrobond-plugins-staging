# Macrobond: hosts with widgets

How to handle a request that uses the Macrobond MCP server, which searches and retrieves economic and financial time series: the workflow, searching, choosing series, confirming and fetching, and presenting the answer.

This is the guidance for a host that shows widgets: `render_series_picker`, `render_chart` and `render_table` are in your tool list.

## Contents

- The reference
- Workflow
- Searching
- Choosing series
- Confirming and fetching
- Presenting
- Closing line

## The reference

The facts about the tools and the data - filters and codes, return fields, confirmation rules, data quirks, house style - are in the server's reference. When `get_macrobond_reference` is in your tool list, call it with topic `search` before you search, `data` before you recommend series, `retrieval` for vintages or revisions, and `style` before building your own chart or table. Otherwise call `get_instructions` with no topic, then with topic `search`, and follow what they return; load topic `selection` before you recommend series, `retrieval` for vintages or revisions, and `presentation` before answering with data.

## Workflow

Follow these steps in order for every data request.

1. **Clarify before searching.** Unless the request names measure and region, ask the one or two questions that actually change the search - which measure, which region, what period, annual or monthly. "Give me GDP", for example, names no region: ask before searching. Ask them together, in prose, and wait - don't guess your way into a search whose results you then have to walk back.
2. **Search once per concept, in parallel:** 1-3 keywords in `text`, country/frequency/source in `must_have_values`. Read the results; do not list them.
3. **Let the user choose.** Open exactly one `render_series_picker` with the `search_id`(s) and the names you recommend; their submit confirms those series.
4. **Fetch what they chose, then show it** with `render_chart` and/or `render_table`. Do not retype fetched numbers in prose.

Throughout:

- Judge a series by its metadata (`FullDescription`, `Frequency`, `Source`), never by its code.
- Read the data quirks before you recommend series: reference topic `data` (`selection` with `get_instructions`).
- Compute transforms such as YoY from fetched values yourself, once search shows Macrobond does not publish them.
- In answers that use Macrobond data, show the series codes used and end with the closing line below.

## Searching

Be efficient without trading accuracy: run independent searches and read-only calls in parallel, and keep every metadata read and validation check that prevents a wrong result. Don't ask the user the same thing twice. Discovery should trigger **zero fetches** - metadata comes free with every search.

### Search text

Never pass a natural-language phrase or the user's raw wording to `search_entities`. Split every request into slots before searching:

- the measure's canonical name, in as few tokens as name it (1-3: `"CPI"`, `"core PCE"`, `"unemployment rate"`), goes to `text`
- country / region, frequency and source go to `must_have_values`
- transform, adjustment and interpretation words are dropped - compute, or read from results

Then:

- **Keep words that identify the measure:** "core", "headline", "price index", "rate", "real"/"nominal". "PCE price index" keeps "price index" even though "index" looks like a qualifier.
- **Strip** country names, frequency words ("monthly", "quarterly"), provider names, transform and adjustment words ("year over year", "YoY", "y/y", "month over month", "change", "growth", "annualized", "annual rate", "seasonally adjusted", "SA", "NSA"), and vague interpretation words - **especially "inflation"**. Search the index name (`"CPI"`, `"PCE price index"`, `"HICP"`) instead.
- A user who wants a pre-computed transform still gets a search on the measure name; identify the YoY variant from `FullDescription`.
- "US 10-year Treasury yield": search `"Treasury yield"` with `Region="us"` and pick the 10Y tenor from the results. "Euro area real GDP growth": search `"real GDP"` with `Region="eu"` and compute growth from the level.
- On zero results, **shorten** to the single most distinctive token before concluding anything.
- When the user's wording finds nothing, retry with the producer's or methodology's own term, or discover the wording with a facet search (see "Before concluding data is absent").

### Filters

- Never infer region, concept or frequency from a series code - read the metadata.
- Discover a `RegionKey` from a reference series; never construct one by analogy. Niche concepts may be untagged - fall back to text plus `Region`.
- Prefer RegionKey-tagged series, then RegionVariable-only, then untagged.
- Verify a concept's frequency by inspecting a representative series rather than assuming.
- `Class` (stock vs flow) is rarely a useful filter.
- Read a `Release` name off a neighbouring series. For `Estimator`, enumerate values with `list_attribute_values` and read them off a representative series rather than guessing.
- **Verify, don't guess.** When unsure of a value, call `list_attribute_values(attribute="X")`; use `get_attribute_information(attributes)` only if that reports the attribute unknown. Skip the lookup only for a value documented in the reference or a standard code you are certain of (`us`, `gb`, `monthly`); never guess a value to save a call. Guessed Region codes, constructed RegionKeys and invented attribute values are the common mistakes.

### Reading results

- Read `candidates` to decide. Pass the picker the `search_id`, not the candidates.
- Use the returned PrimName in follow-up calls, not the alias you searched.
- For revision timestamps, use `get_revision_info` on the series the user actually chose.
- For metadata such as `RegionKey`, `DisplayUnit` or `EndDate`/`LastValueDate`, read the search response; don't call `fetch_entities` for it.
- Searches you run only to read metadata - name recovery, RegionKey discovery, freshness or source checks - are lookups, not choices. Read them and move on. Don't open a picker on them - that asks the user to act on a lookup they were never party to.
- **A user may want only the series codes**, to use elsewhere. Then search, identify, present and stop - no fetch. Stop after the picker, or after the search with a written answer when they only want to know what exists.

### Result count

When unsure, use the default `limit` (50).

- **Known-item lookup** (indicator plus country, or a code): **5-10**. You expect one or two correct hits; reading more is wasted context.
- **Concept resolution** ("US inflation", "UK unemployment"): the default **50**, so headline/core, SA/NSA and source variants all surface.
- **Exploration or set construction**: up to **100**, always with tight filters, to map the space rather than miss members.

**Read at least two pages before shortlisting** whenever `has_next` is true - call `search_entities` again with `page=2`, same arguments. The user sees only what you recommend or shortlist, so a measure at rank 30 that you never read is one the user cannot reach without another turn. This is a floor, not a target: stop at two unless you have a specific reason to go further, and never walk a match set to its end. Results are ordered by text relevance and relevance decays, so page 3 is rarely where the right series was hiding. If the right series is not in the first hundred the fix is a narrower search, not a deeper one. `has_next` and `is_truncated` call for opposite moves: when `is_truncated` is true, the two-page floor does not apply - narrow the search instead of paging, because more pages cannot complete a capped set.

### Errors

- **Auth error:** you cannot fix it; stop and tell the user to check the connection.
- **`HTTP 404 "Unknown attribute value: X=y"`:** call `list_attribute_values(attribute="X")` and retry with a canonical value.
- **`HTTP 404 "Unknown attribute: X"`:** check the name with `get_attribute_information`, then retry.
- **`HTTP 500` on a very broad query:** narrow with filters or lower the scope; do not retry unchanged.
- When an error comes from parameters you supplied, correct them and retry without surfacing the raw error. Show the user only errors they must act on.

### Before concluding data is absent

A false "not available" costs more trust than a broad candidate list. Run these in order before saying a series does not exist, before asserting a pre-computed version is absent, and before computing a metric yourself. A failed first guess at the name, or a zero text-search result on its own, is not evidence the series is absent. **Finding a related series - the raw inputs, say - does not count as searching for the specific one.**

1. **Shorter text** - the single most distinctive token.
2. **Provider or methodology terminology** - the survey's name, the model's name, the metric's formal label, rather than the user's interpretive wording. A user naming a standard method ("futures-implied", "futures-based") is usually pointing at a published product, not asking you to recompute it.
3. **Concept plus region** - for a named survey, index or product, the underlying concept in `text` filtered by `Region` rather than the proper noun.
4. **Facet browse, no text** - search by `Source`, `Category` and/or `Release`, with facet values verified first with `list_attribute_values` (or read off a neighbouring series' metadata). Read the `FullDescription`s to learn the family's vocabulary and naming pattern; one foothold is enough to find the rest.

Only when all four come back empty is "not available", or your own computation, the answer.

### Too many results

When `is_truncated` is true, the goal is to make the right series rank into the top results. Never present a truncated set as complete. Narrow in this order:

1. Add filters - `Region`, then `Frequency`, then `Source`. Verify values with `list_attribute_values`.
2. Apply `filter_suggestions` when present.
3. Drop the text and search by `RegionKey` when you know the concept.
4. Add `Category` or a sub-national attribute.
5. Raise `limit` (up to 100) only after filtering, when you genuinely need to see more of an already-narrowed set.

If a concept legitimately spans many series, partition it (one search per `Region`) rather than relying on one capped set.

## Choosing series

### The one distinction

Search results are ranked by text relevance, not economic significance. Use economic knowledge to decide *what to search for* and to give the user context - not to silently pick the answer. Draw one line:

- **Distinct measures** - a different concept or definition: CPI vs PCE, headline vs core, a level or volume vs an index. When more than one plausibly fits and the user has not said which, put them all in front of the user - in the picker's `recommended` list - with your reasoning. Do not choose for them.
- **Secondary attributes** - the same measure in a different encoding: SA vs NSA, source, frequency, tag precedence, index vs price-level form. **Resolve these yourself** with the defaults below.

### Selection defaults

These resolve secondary attributes once the measure is settled. They never choose between distinct measures.

- **Seasonal adjustment:** SA over NSA for macro analysis, unless the user asks for NSA.
- **Scope:** the headline or total index over a narrow sub-component. Headline vs **core** is a distinct-measure choice - offer both, never silently drop core.
- **Source:** the national statistics office or central bank over an international organisation.
- **Policy reference:** when the policymaker's target differs from the most-cited measure, note both; if the user has not specified, present both rather than silently leading with one.
- **Harmonised vs national:** pick one and use it consistently; never mix harmonised and national in one labelled set.
- **Composite concepts** (trade balance, current account, fiscal balance): the aggregate, noting components (exports, imports) exist.
- **Tag precedence:** RegionKey > RegionVariable > untagged.
- **Price indices:** the SA index form over a raw currency-denominated average price.
- **Production:** when the user asks for production (barrels, tonnes, units), rank mass/volume/count `DisplayUnit`s above index series; return an index only if no volume series exists or the user asked for one.
- **Reported over modelled:** prefer reported series for live tracking and policy reference.

**Explicit constraints are requirements, not tie-breakers.** "Most up to date", "most recent", "highest frequency", "most granular", "by state/city/sector": for recency, compare `LastValueDate`/`EndDate` across one series per candidate source before committing - never commit on name recognition or result-count prominence; for granularity, prefer sub-national, commodity-level or sector-level providers even when less familiar than the headline aggregate.

**Source checks before committing.** Result count is not a quality signal. Rank sources by `LastValueDate`/`EndDate` when recency matters; cross-reference an unfamiliar or unexpectedly dominant source externally and note the origin so the user can overrule; for timely cross-country views, check national freshness in parallel and switch sources when an international aggregator is stale.

### Kinds of request

| Type | Signal | Approach |
|---|---|---|
| Known-item lookup | names indicator plus country, or gives a code | text search to identify |
| Concept resolution | names a concept, not the measure ("US inflation") | enumerate measures, targeted searches, recommend the candidates |
| Set construction | the same data across a group ("inflation in G7") | reference series, RegionKey, fan out |
| Exploratory | a topic without a measurable concept ("recession indicators") | decompose, broad searches, overview |

Requests often combine types: "G10 central-bank inflation" is set construction plus per-country concept resolution.

### Known-item lookup

Short tokens in `text`, the rest in filters - `search_entities(text="core CPI", must_have_values={"Region": "gb", "Frequency": "monthly"})`; identify variants like YoY from `FullDescription`. For a **series code**, `search_entities(text="<code>")` confirms it exists and returns its metadata. If nothing comes back and you know the provider, retry with `must_have_values={"Source": "<provider>"}`. If that also finds nothing, the code may belong to an external provider and not be indexed natively - tell the user.

### Concept resolution

1. **Enumerate the standard measures first** from your own knowledge - a price concept usually has several indices and deflators, each headline and core. A generic search ranks by text relevance and silently omits measures the user expects.
2. **One targeted search per measure** (not one broad search), each with a Region filter, in parallel: `search_entities(text="core PCE", must_have_values={"Region": "us"})` and `search_entities(text="CPI", must_have_values={"Region": "us"})`.
3. Confirm what each measures from `FullDescription`, `Source`, `Frequency`.
4. Apply the selection defaults to secondary attributes, and exclude modelled series.
5. **One picker across those searches**, with every distinct measure the user is likely to want. A measure you leave out is one the user cannot reach without another turn.

- **"What do you have?" variant** ("what unemployment data do you have for Germany"): the user asked what exists, so pre-selecting would answer a question they didn't ask. Search broadly by concept plus Region. Open the picker with `recommended` omitted - the top matches arrive unticked; `render_series_picker` has its own `limit` for this (default 20). Name the measure types you can see in your reply - the picker itself shows a flat list - and note truncation.
- **Composite concepts:** return the aggregate; if several compositions exist (goods only vs goods and services), explain and offer both.
- **Classification bundling:** when a specific commodity or item resolves only to a broader chapter (HS/SITC for trade, COICOP for prices, NACE for activity), say so explicitly, check more granular national customs or statistical providers, and search related classification codes.

### Set construction

1. **Resolve the group into countries.** Expand group labels (Nordics, G7, emerging markets) to an explicit country list and state the expansion. Standard groups (G7, G20, EU, Nordics, BRICS) have conventional membership; state your reading of ambiguous ones.
2. **Find one reference series**, e.g. `search_entities(text="CPI", must_have_values={"Region": "us"})`.
3. **Read its `RegionKey`.**
4. **Fan out**, with a `Frequency` filter for comparability: `search_entities(must_have_values={"RegionKey": "<key>", "Region": ["us","gb","de","fr","it","jp","ca"], "Frequency": "monthly"})`.
5. **Check coverage** against every requested country. If one is missing: retry the NSA variant, then the unsuffixed base key, and note any SA/NSA mix; then a per-country text search, `search_entities(text="<concept>", must_have_values={"Region": "<country>"})` (required for derived or growth-rate series, which generally lack RegionKeys); then report the gap. **Never silently return a partial set.**
6. Several matches for one country: the most authoritative source.
7. Run the validation checks below before presenting.

- **Supranational regions:** verify that a supranational Region code (Europe, euro area) exists for the concept before relying on it. For bond yields, which have no single issuer, pick a representative sovereign and offer alternatives.
- **Sub-national geography** (city, state, province, district): prefer the country's `city_*`/`state_*`/`region_*` attribute codes (enumerate with `list_attribute_values`, then filter by code) over place names in text.

### Exploratory questions

1. Decompose the topic into measurable sub-concepts (UK recession leading indicators: PMI, yield curve, consumer confidence, building permits, initial claims).
2. Run up to three or four short-keyword searches in parallel, each filtered by Region.
3. Group results into three to five categories using `FullDescription` and `Source`.
4. Present a map with one or two representative series per category (name, description, frequency, source) - not an exhaustive dump.

If a search is too broad, note the count and suggest narrowing; if too narrow, broaden or drop the Region filter.

### Advanced patterns

- **Per-country concept variation** ("what does each G10 central bank target?"): one RegionKey across regions fails, because institutions target different measures. Resolve the group, use domain knowledge for each country's policy-reference measure, run separate concept searches in parallel, confirm via metadata, give per-country rationale.
- **Parameter sets** ("build a US yield curve"): search broadly in the region; look for a maturity attribute in results and `filter_suggestions` and enumerate it with `list_attribute_values`, else read maturities from `FullDescription`; take one series per point; **exclude implied or interpolated tenors** unless the user asks for a complete interpolated curve; when a point is missing, try the naming variants a present neighbour uses; check name and description agree; report points still missing or discontinued; order by ascending maturity, not API rank or alphabetical. For fixed-expiry families, iterate the expected range.
- **Multi-sector surveys** asked for "key indicators" or an "overview": the headline composite for **each** sector **and** the sub-indices for **every** sector, not just the lead one. Composites for all but sub-indices for one is incomplete.

### Handling data quirks

The quirks themselves are described in the server's reference (topic `data`; `selection` with `get_instructions`). They cause silently wrong results and don't show up in a single search or metadata inspection, so check them before finalising a candidate set. What to do about each:

- **Regime-dependent indicators:** for "the" indicator, pick the operative one as primary and offer the others only for cross-period or historical use.
- **Modelled, derived and academic-research series:** judge from `FullDescription` and `Source` whether a series is reported or modelled, and prefer reported series for live tracking and policy reference. Treat words like "Shadow Rate", "Implied", "Interpolated", "Modelled", "Nowcast" as a prompt to check, not an automatic filter - "Estimate"/"Estimated" and "Implied" also tag market-derived series a user may have asked for (futures-implied probabilities, survey-based estimates). Exclude a model output the user did not request; keep it when the estimate is the request.
- **Identifier conventions within a family:** try pattern variants and read a present peer's name and metadata from the search results (no fetch needed) before reporting a gap.
- **Structured identifiers:** absence from text search is a weak signal for structured families. Find one member another way - a concept search or a neighbouring series; if that also finds nothing, a facet browse on `Source`/`Category`/`Release`/`Estimator` with no `text` - learn the pattern, construct the rest.
- **Name vs description mismatch:** flag it rather than pick.
- **Time-base mixing:** look for "AR", "Annualized", "Annual Rate", "Change P/P", "Change Y/Y" and similar in `FullDescription`; convert, substitute or exclude. Prefer a single harmonised dataset with one time base over stitching national-source series.
- **Cross-country traps:** prefer one harmonised provider where methodology matters (fiscal, debt, prices, labour). Verify coverage before publishing a labelled "G7" or "EU" set, fill a missing country with the closest national or annual equivalent and flag the frequency or lag difference. Never silently omit a country. Check `FullDescription` for observation-type markers ("Change P/P", "Change Y/Y", "Moving Average", "Growth Rate" and similar); when one country's "SA" variant is a level and another's a change or moving average, flag it or fall back to the NSA level with a note.
- **Stale benchmarks:** check `LastValueDate`/`EndDate` on every series used for live tracking; offer a timely alternative when one has gone stale. When hunting a stale or replaced benchmark, search with `include_discontinued=true`.

### Validation checks

Before presenting a set:

- **Name and description agree** for parameter-indexed series.
- **One observation type** (level vs change vs growth) across a cross-country set; flag mismatches.
- **One unit basis** - no annualized rates mixed with period flows (factor-of-4 errors).
- **One frequency** in a comparison set, or the mismatch flagged.
- **Magnitude and sign** - once values are in, a value an order of magnitude off the set's range, or opposite-signed from the rest, is almost certainly a mixed observation type. Exclude, substitute or flag it.

## Confirming and fetching

**Search before fetch** unless the series name is already confirmed.

### Fetch windows

Pass `start_date` and `end_date` whenever the user named a window. **For "latest", pass today's ISO date as `end_date` on the fetch tools; with `render_chart`/`render_table`, leave `end` off unless the user asked for an end.**

**Size the window to what you will display.** A trend answer: `start_date="-1y"`. A latest-value or cross-sectional answer: the smallest window that still holds one non-null observation - daily about `-10d`, weekly `-6w`, monthly `-3m`, quarterly `-2q` - and widen only when history is needed. A year of daily data to print one number wastes payload, context and time.

When a fetch lists names that need confirming, confirm them through the picker - the `search_id` that found them, with those names in `recommended` (re-run the search if it is more than an hour old) - then retry after the submit.

### The picker

`render_series_picker` is how you present search results at all. Not a markdown list: a list the user cannot act on makes them retype series codes you already have. The picker serves both decisions at once: which measure the user means and which series to fetch. Its rows already carry code, title, source, frequency and freshness, so never print the same table beside it.

1. **Search, then read.** Keep the `search_id` from every search this question needs.
2. **Open exactly one picker** with `render_series_picker(search_id=<one id or a list>, recommended=[names])`. When the answer spans separate searches - CPI and PCE, inflation and unemployment, or one region per search - pass all their ids in this one call; never open one picker per search. Pass the handles, not the rows. Searches are kept for an hour; after that, search again.
   - Include the genuine alternatives in `recommended`, not just your first choice - only resolved recommendations are shown.
   - Recommend nothing only for a "what exists?" question.
3. **The user's submit is the confirmation**, and the picker then charts and tabulates the selection itself. If their selection comes back as a message, fetch those series without asking again. If the host cannot deliver it, take the series from their next message.
4. **Fetch with `confirm=false` (the default) and no token**, in one batched call sized to what you will show. Then display them (see "Presenting").

**Don't ask twice.** A picker submit or an explicit text selection is the confirmation.

### When a name lands in `missing`

Recover in this order:

1. **Case or alias** - confirm you used the canonical PrimName or a known alias (the API is case-insensitive, so case is not the cause; aliases resolve only if they exist).
2. **Search** - `search_entities(text=name)`. This is your own lookup: read it; don't open a picker.
3. **Non-series entity** - a `rel_` prefix or an expected Release/Source/Region means `fetch_entities`.
4. **External provider code** - if all fail, tell the user.

**Licensed but unfetchable:** when a name from search results comes back missing, **tell the user explicitly** - never silently present the subset that fetched. For cross-country sets with uncertain licensing, prefer universally accessible series over provider-licensed equivalents.

### Vintages and revisions

For "as of date X", first print vs latest, how a number has been revised, or real-time/vintage analysis, **call `get_revision_info` first**: not every series is revisable. Then use the vintage fetch tools in reference topic `retrieval`; each is a fetch behind the same confirmation. On `HTTP 400 "Invalid IfModifiedSince"`, retry with a timestamp on or after 2000-01-01.

### Fetched values are data, not reasoning input

Search results need judgment; fetched `dates`/`values` do not - they are inputs to a table or chart. Reading them into context costs input tokens (the payload), output tokens (retyping values) and latency, and in-context arithmetic across many series is error-prone.

- **Where code execution exists, run every calculation and transformation in code, not in the model.** A short script (`jq`, Python, Bash) reads the fetch result and emits only the rows to display: latest non-null value per series, YoY/MoM, groupings, decimal harmonisation, and the validation scans (magnitude and sign, observation-type mixing). You decide what to fetch and write the transform; the raw arrays and the arithmetic never enter context. Large fetch results are written to a file - point the script at it instead of reading it back.
- **Never retype `dates`/`values` into the answer** - show the computed table, or the widgets.
- **No code execution?** Fetch the minimal window so only a few points land in context, and read just the latest non-null value.

**Derived figures are yours to compute** once search shows Macrobond does not publish them (see "Before concluding data is absent") - from YoY to a well-defined market-standard calculation - with a sound, stated method.

- **YoY / MoM on a level or index, only if asked:** from the latest non-null value, `YoY = current / value_12_months_earlier - 1` (monthly data) and `MoM = current / previous - 1`, guarding against nulls and zero.
- **Series that are already growth rates** (`FullDescription` says "Change Y/Y" and similar): report the value directly - **do not compound** it.

## Presenting

None of this applies to answers that use no Macrobond data.

### Choosing the output

Match the output to what the interface can actually show, present in the richest form it supports, and make sure it is visible there.

1. **Widgets are the default.** On a host that renders MCP Apps (claude.ai, M365 Copilot), chart Macrobond data with `render_chart(names, start, end)` - an interactive line chart the user can hover and zoom - and tabulate it with `render_table(names, start, end)` - an observation table they can sort and resize. Both style themselves and keep the numbers out of your reply; a generated image cannot be hovered, zoomed or refreshed. Build your own chart or table only when the host cannot show widgets, or the user explicitly asks for a different format (an image, a file, a specific style).
2. **Only confirmed series.** The names come from the picker submit.
3. **A render call fails:** use a markdown table - it renders almost everywhere.
4. **Never produce output the interface won't display** - a chart file or HTML nobody will see. Fall back to the best form that shows.

**Opening the series in Macrobond.** When the user asks for the link or to open the series in Macrobond Analysis, open `render_series_picker` again with the same `search_id` (re-run the search first if it is more than an hour old) and the series the user selected as the recommended names; the user confirms there and uses the chart's *Open Series in Macrobond Analysis* button. Do not call `create_series_preview_url` yourself.

By data shape, when there is more than one series or observation worth showing:

- **Table** - latest values, cross-sectional comparisons, few observations, anywhere exact figures matter.
- **Chart** - trends: long windows, several series over time, turning points, divergences and regime shifts that a table buries.
- **Both** - the normal ending for a "how do these relate" question, and for a trend question where exact values also matter: chart for the shape, table for the exact figures.

Chart type by data shape, for a chart you build yourself:

- **Line** - a time series (level or rate) over time; the default for macro series.
- **Multi-line** - comparing a few series over time.
- **Bar** - cross-sectional comparison at one point in time (latest value by country), or period-over-period changes.
- **Reference line** - overlay a target or threshold (an inflation target, the 50 PMI line) as a dashed horizontal line with a label.

Never ship a chart without a title and source attribution.

Keep to four series or fewer in one chart for readability, and put only comparable units on one value axis - otherwise draw separate charts. `render_chart` has no reference-line overlay - describe a threshold in prose.

### What the answer contains

- **Every series code used.** Inline next to its figures, or a compact `code | title | source` table at the end when the layout can't carry them (a chart or a prose summary). The user needs codes to verify, reproduce or re-fetch.
- The canonical PrimName and `FullDescription`, latest observation date and value, frequency and region.
- A one-line reason for each choice, and the alternatives when ambiguity remains.
- Multi-series results in their natural order: ascending maturity for curves, alphabetical by country for cross-region sets.
- **Your prose says what the numbers mean;** the widgets or the table carry the numbers. Keep the written answer around them short.

Formatting rules:

- **Table schema:** the Series column holds only the bare PrimName; measure, adjustment and source go in their own columns.
- **Observation window:** about the 12 most recent points for monthly or quarterly series; a compact recent window, or a chart, for daily or weekly. Honour explicit windows ("last 6 months" is `-6m`, "past 4 years" is `-4y`, "latest value only").
- **Decimals:** one precision across a set - the largest meaningful number of decimals present. Don't hardcode two decimals; no artificial decimals on whole numbers.
- **Staleness:** flag a daily series more than 10 business days old, monthly more than 2 months, quarterly more than 6 months: "Latest value: {date} - note: this series has not updated since {date}, it may be discontinued." Offer an alternative when there is one.

### Source attribution

Responses mix Macrobond data with analyst commentary - make provenance clear. Add a footnote legend:

```
| Country | Series ^[MB] | Latest ^[MB] | Target ^[Analysis] | Assessment ^[Analysis] |
|---|---|---:|---|---|
| US | <series> | 2.8% (Feb 2026) | 2% | Above target |

*^[MB] = name, value, date from Macrobond API. ^[Analysis] = domain-knowledge interpretation.*
```

Every claim must be traceable to either Macrobond data or domain-knowledge analysis.

### Charts and tables you build yourself

For a chart, table image or report you generate yourself from Macrobond data - not the widgets, which are already styled - apply the Macrobond house style from the server's reference (topic `style`; `presentation` with `get_instructions`). If the user asks for a different style, follow the user, and say where it departs from the Macrobond style. Skip styling for plain data lookups.

- Start charts from `get_plotly_template()` / `apply_mpl_style()` when those helpers are available.
- **Consistency first - it overrides everything.** Within one deliverable built from Macrobond data, every chart, table and text block uses the same fonts, sizes, title treatment and source and logo placement. Pick a style for the first chart and apply it to all.
- Keep to about four colours in one chart where you can.
- Place standalone exports on white.
- Use Arial for everything generated programmatically from Macrobond data - Aeonik is licensed, not installed, and silently falls back and breaks the brand.
- Write the company name as **Macrobond** - never MACROBOND, macrobond or MacroBond.

## Closing line

End an answer that uses Macrobond data with this exact line, after any attribution legend: "Macrobond MCP relies on your chosen AI Tool and LLM, which may produce errors. Please verify your outputs before relying on them."
