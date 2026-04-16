---
layout: post
title: HW5 — UFO Sightings Visualizations
author: Yifeng Luo
---

<!-- ===== PLOT 1 ===== -->
<div class="left">
{% include elements/button.html link="https://github.com/UIUC-iSchool-DataViz/is445_data/raw/main/ufo-scrubbed-geocoded-time-standardized-00.csv" text="The Data" %}

{% include elements/button.html link="https://github.com/ivonne-code/ivonne-code.github.io/blob/main/hw5.1_ufo.ipynb" text="The Analysis" %}
</div>

## Visualization 1 — Geographic Distribution of UFO Sightings by Shape

<vegachart schema-url="{{ site.baseurl }}/assets/json/ufo_map.json" style="width: 100%"></vegachart>

The first visualization maps approximately 6,000 sampled UFO sightings (filtered to the
continental United States) as a geographic scatter plot, where each point represents a
single reported sighting. I restricted the data to the top 10 most frequently reported UFO
shapes — such as "light," "triangle," "circle," and "fireball" — to keep the legend readable
and the visual meaningful. The x-axis encodes **longitude** (quantitative) and the y-axis
encodes **latitude** (quantitative), effectively placing each sighting on a pseudo-map of
the US. Shape is encoded using **color** (nominal), using Vega-Lite's `tableau10` color
scheme, which provides 10 visually distinct hues well-suited for categorical variables — no
two shapes share a similar color, making them easy to distinguish. No sequential or
diverging scheme was appropriate here since shape carries no inherent order or magnitude.

On the data transformation side, I loaded the raw CSV (which has no header row) and
manually assigned column names. I then filtered to US-only sightings (`country == 'us'`),
coerced latitude, longitude, and duration to numeric types (dropping rows where conversion
failed), applied a continental US bounding box filter (roughly 24°–50°N, 125°–66°W) to
exclude outliers and non-continental points, and finally parsed the datetime column to
extract a `year` field for use in Plot 2. I then kept only rows whose shape appeared in the
top 10 by frequency and drew a random sample of 6,000 rows to prevent the vega-lite JSON
spec from exceeding GitHub's file size limits.

**Interactivity:** The chart uses a `selection_point` on `mouseover` bound to the `shape`
field. When you hover over any point, all sightings sharing that shape are highlighted —
they enlarge and reach full opacity — while all other shapes fade to light gray. This makes
it easy to see where a particular shape clusters geographically without cluttering the chart
with separate facets. A tooltip additionally reveals the city, state, shape, year, and
duration (in seconds) for each individual point, providing granular detail on demand without
overloading the base view.

---

## Visualization 2 — Annual UFO Sighting Counts (1950–2014)

<vegachart schema-url="{{ site.baseurl }}/assets/json/ufo_yearly.json" style="width: 100%"></vegachart>

The second visualization shows how the total number of UFO sightings reported in the US
changed from 1950 to 2014. Each bar represents one calendar year, with the x-axis
encoding **year** (ordinal, labeled every 5 years to avoid crowding) and the y-axis encoding
**count of sightings** (quantitative). I additionally encoded count with a sequential `blues`
**color scheme** so that heavier reporting years stand out visually — darker bars correspond
to higher counts. This sequential scheme is appropriate because the variable (count) is
ordered and one-directional; a categorical scheme would imply that years are unrelated
groups, which is misleading for a temporal series. The steep rise starting in the mid-1990s
and peaking around 2012–2013 is clearly visible, likely reflecting both increased public
interest and the growth of online reporting platforms.

For data transformation, I reused the cleaned dataframe from Plot 1 (already filtered to US,
year parsed), then further restricted to the 1950–2014 window to remove a small number of
anomalous records outside the plausible reporting era. I then used a `groupby('year').size()`
aggregation to produce a two-column summary dataframe (`year`, `count`) that Altair
could render directly. No normalization was applied since absolute counts are the
meaningful quantity here. Tooltips on each bar display the exact year and sighting count,
making it easy to read precise values without overloading the y-axis tick density.
