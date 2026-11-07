---
name: "UFO Reports: Timing and Duration"
tools: [Python, Altair, Vega-Lite]
image: assets/pngs/hw5_ufo_monthly.png
description: "Explore when U.S. UFO reports occurred and compare reported durations across shapes and periods."
permalink: /projects/hw5-ufo-reports/
---

# UFO reports: timing and duration

**Keliang Zhu · IS445 · Homework 5**

These two views explore **50,849 U.S. reports from 2000–2013** in the course UFO dataset. The end date avoids the partial year 2014. Counts describe this collection of reports; they do not measure verified events or population risk.

<p>
  <a class="btn btn-outline-primary mr-2" href="https://raw.githubusercontent.com/UIUC-iSchool-DataViz/is445_data/main/ufo-scrubbed-geocoded-time-standardized-00.csv">The Data</a>
  <a class="btn btn-outline-primary" href="https://github.com/Keliang23/Keliang23.github.io/blob/main/python_notebooks/HW5_UFO_Reports.ipynb">The Analysis</a>
</p>

## 1. When were reports observed?

<div class="hw5-chart-wrap"><div id="hw5-monthly" aria-label="Heatmap of U.S. UFO report counts by year and month"></div></div>
<p id="hw5-monthly-status" role="status">Loading monthly report counts…</p>

The heatmap shows the number of reports for each month and year. The horizontal axis encodes month as an ordered category, and the vertical axis encodes year in chronological order. Color encodes the quantitative count: the sequential blue scale moves from pale blue for low counts to dark blue for high counts, which fits a measure with no meaningful midpoint. In Python, I read the headerless CSV with explicit column names, parse the observation dates, normalize the country labels, and filter to records labeled `us` from 2000–2013. I then count rows by year and month and fill the complete 14-by-12 grid, using zero for any unobserved combination. Every shape category is included in this view. July 2013 has the largest monthly count, at 775 reports. The darker cells in later years describe this dataset's reporting pattern, which may also reflect changes in coverage or reporting habits.

## 2. How long did reports of different shapes last?

<p><strong>Try it:</strong> change the Period dropdown below the chart. Hover over a dot or line for the sample size and quartiles.</p>
<div class="hw5-chart-wrap"><div id="hw5-duration" aria-label="Interactive comparison of reported UFO durations by shape"></div></div>
<p id="hw5-duration-status" role="status">Loading duration comparison…</p>

This chart compares reported durations for the eight most frequent named shapes with valid durations in the same U.S. population. Shape is nominal on the vertical axis, while horizontal position encodes quantitative duration in minutes on a logarithmic scale: equal distances represent equal ratios. Each dot marks a median, and its horizontal line spans the 25th to 75th percentiles. All marks use teal because the shape labels already distinguish the categories; a separate color scale would add little information. In Python, I convert numeric seconds to minutes, retain finite positive durations, remove missing, blank, `unknown`, and `other` shape labels, and select the top eight shapes across the full period. This leaves 35,417 reports. I compute each shape's median and quartiles for all years and for three periods, without imposing an upper duration cutoff. Medians limit the influence of very long reports: for all years, fireball reports have a two-minute median, triangle reports about 2.58 minutes, and the other six displayed shapes three minutes. The intervals show the middle 50% of reported durations, not confidence intervals.

### What the interaction adds

The **Period** dropdown filters the second chart to all years, 2000–2004, 2005–2009, or 2010–2013. It updates every shape's median, interval, and tooltip, making it easier to see whether a full-period pattern also appears within a shorter period. Shape order and axis limits stay fixed, so the marks remain comparable as the selection changes. Both charts provide exact values on hover. The charts are not linked, and the dropdown changes the data shown rather than merely zooming or panning.

Reported shape and duration are not independently verified, and numeric durations may disagree with the original text descriptions. Missing country values are excluded rather than inferred. The three periods have different lengths, so their sample sizes are not directly comparable reporting rates.

<style>
  .hw5-chart-wrap { overflow-x: auto; margin: 1.5rem 0 .75rem; padding: 1rem; background: #fff; border: 1px solid #e2e8ed; border-radius: 6px; color: #17232d; }
  .hw5-chart-wrap .vega-bindings { padding-top: .8rem; font-size: 15px; }
  .hw5-chart-wrap select { padding: .3rem .5rem; margin-left: .35rem; border: 1px solid #8797a3; border-radius: 4px; background: #fff; color: #17232d; }
</style>

<script src="https://cdn.jsdelivr.net/npm/vega@5.30.0/build/vega.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-lite@5.20.1/build/vega-lite.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-embed@6.26.0/build/vega-embed.min.js"></script>
<script>
  const hw5Charts = [
    ['hw5-monthly', '{{ "/assets/json/hw5_ufo_monthly.json" | relative_url }}'],
    ['hw5-duration', '{{ "/assets/json/hw5_ufo_duration.json" | relative_url }}']
  ];
  hw5Charts.forEach(([id, url]) => {
    const status = document.getElementById(id + '-status');
    if (typeof vegaEmbed !== 'function') {
      status.textContent = 'The chart library could not load. Please refresh the page.';
      return;
    }
    vegaEmbed('#' + id, url, {actions: false, renderer: 'svg'}).then(() => {
      status.hidden = true;
    }).catch(error => {
      status.textContent = 'This chart could not load. Please refresh the page.';
      console.error(error);
    });
  });
</script>
