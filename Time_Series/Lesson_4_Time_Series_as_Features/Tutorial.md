Some time series properties can only be modeled as *serially dependent* properties, or using as features the past values of the target series. The structure of these may not be as apparent from a plot over time, but would be when plotted against past values.

The goal here is to train models to fit curves to plots which exhibit serial dependence.

---
One common way for serial dependence is to manifest is in **cycles**. These are patterns of growth and decay in a time series that are associated with how the value in a series depends on values at previous times, but not necessarily on the time step itself. Cyclic behavior is characteristic of systems that can affect themselves or whose reactions persist over time.

What distinguishes cyclic behavior from seasonality is that cycles are not necessarily time dependent. What happens in a cycle is less about the particular date of occurence, and more about what has happened in the recent past.

---
To investigate possible serial dependence in a time series, we need to create lagged copies of the series.  By lagging a time series, we can make its past values appear contemporaneous with the values we are trying to predict.

A **lag plot** shows its values plotted against its lags. Serial dependence will often become apparent by looking at a lag plot.

The most commonly used measure of serial dependence is **autocorrelation**, which is the correlation a time series has with one of its lags.

---
**Partial autocorrelation** tells you the correlation of a lag accounting for all of the previous lags- the amount of "new" correlation the lag contributes. Plotting this can help you choose which lag features to use. This kind of plot is known as a *correlogram*.

---
Be mindful that autocorrelation and partial autocorrelation are measures of *linear* dependence. Because real world time series often have substantial non-linear dependences, it's best to look at a lag plot when choosing lag features.

---
Example: Forecasting the number of flu cases for the coming weeks.

*Flu Trends* shows irregular cycles instead of regular seasonality. Modeling these with lag features will allow our forecaster to react dynamically to changing conditions instead of being constrained to exact dates and times as with seasonal features.

Look at the lag and autocorrelation plots first:
```python
_ = plot_lags(flu_trends.FluVisits, lags=12, nrows=2)
_ = plot_pacf(flu_trends.FluVisits, lags=12)
```

Lag a time series in Pandas with the `shift` method:
```python
def make_lags(ts, lags):
    return pd.concat(
        {
            f'y_lag_{i}': ts.shift(i)
            for i in range(1, lags + 1)
        },
        axis=1)


X = make_lags(flu_trends.FluVisits, lags=4)
X = X.fillna(0.0)
```

When using lag features, you are limited to forecasting time steps whose lagged values are available.

You can search for *leading indicators* to improve the forecast. 
```python
search_terms = ["FluContagious", "FluCough", "FluFever", "InfluenzaA", "TreatFlu", "IHaveTheFlu", "OverTheCounterFlu", "HowLongFlu"]

X0 = make_lags(flu_trends[search_terms], lags=3)
X0.columns = [' '.join(col).strip() for col in X0.columns.values]

X1 = make_lags(flu_trends['FluVisits'], lags=4)

X = pd.concat([X0, X1], axis=1).fillna(0.0)
```