```python
import plotly.express as px
from gapminder import gapminder
```

## Home Assignments

First, let's create a data frame containing total population per continent. Use only data from years 1992 and newer. 

```python
pop_per_cont = (
    gapminder.loc[lambda x: x["year"] >= 1992]
    .groupby(["year", "continent"])
    .agg(pop=("pop", "sum"))
    .reset_index()
)
```

### Bar chart

Create simple barchart with year on x axis and population on y axis. Use different colors for each continent.

```python
px.bar(pop_per_cont, "year", "pop", color="continent")
```

Try all options for argument `barmode`: `"group"`, `"overlay"`, and `"relative"`. Look at the charts and answer following questions.

- Which of the options is the default one?
- Does this argument affect tick labels on x axis (i.e. the years you see below the chart)? Which one would you prefer?
- Take a look at the ranges of y axes. Which charts are up to 4 billion and which up to 6 billion? Do these numbers have any interpretation?

```python
px.bar(pop_per_cont, "year", "pop", color="continent", barmode="group")
```

```python
px.bar(pop_per_cont, "year", "pop", color="continent", barmode="overlay")
```

```python
px.bar(pop_per_cont, "year", "pop", color="continent", barmode="relative")
```

### Area chart

Change chart type from bar to area. Argument `barmode` doesn't make sense anymore so do not use it.

```python
px.area(pop_per_cont, "year", "pop", color="continent")
```

### Orientation

Flip the axes so that the bars are horizontal. That means we want to have year on y axis and population on x axis. In some cases, plotly figures out (from tha data types) that we want to make a horizontal chart and does it automatically, but that is not our case. Try removing argument `orientation` to see for yourself.

```python
fig = px.bar(pop_per_cont, "pop", "year", color="continent", orientation="h")
fig
```

Note that the latest year is the top one in the chart. If we want to change this behavior the easiest way is to update y axis using `"reversed"` keyword.

```python
fig.update_yaxes(autorange="reversed")
fig
```

This orientation looks like better use of space so we'll be using it in all following exercises.


### True relative

The (default) option `"relative"` creates chart that goes up to sum of all values (about 6 billion in our case). If we wanted the chart to go to 100% (and all years have the same size), we need to prepare extra column in our data

```python
pop_per_cont["rel_pop"] = pop_per_cont["pop"] / pop_per_cont.groupby("year")["pop"].transform("sum")
pop_per_cont
```

Use the newly created column as size of the bar to have uniformly sized bars.

```python
fig = px.bar(pop_per_cont, "rel_pop", "year", color="continent", orientation="h")
fig
```

The chart naturally shows values from 0 to 1. To change it to display percentages we need to specify `tickformat` argument. In this case we want to display percentages with 0 digits after decimal point.

```python
fig.update_xaxes(tickformat=".0%")
fig
```

### Labels, Titles and Captions

To make the chart more readable, replace column names with human-readible descriptions. In case of continents, we can omit the label entirely as the categories itself are self-explanatory so any label would be redundant. Plotly accepts argument `labels` and expects a dictionary. Keys should be column names and values could be any string we want to display.

```python
fig = px.bar(
    pop_per_cont, "rel_pop", "year", color="continent", orientation="h",
    labels={"year": "Year", "rel_pop": "Population (relative to total)", "continent": ""},
)
fig.update_xaxes(tickformat=".0%")
fig
```

Add title and subtitle to the chart. Title usually what kind of data the chart shows ~and subtitle summarizes one key finding~.

```python
fig = px.bar(
    pop_per_cont, "rel_pop", "year", color="continent", orientation="h",
    labels={"year": "Year", "rel_pop": "Population (relative to total)", "continent": ""},
    title="Relative population of continents",
)
fig.update_xaxes(tickformat=".0%")
fig
```

Finally, change type of y axis to `"category"` to change tick values to match actual values in the data. 

```python
fig.update_yaxes(type="category")
fig
```
