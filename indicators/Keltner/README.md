﻿# Keltner Channels

[Keltner Channels](https://en.wikipedia.org/wiki/Keltner_channel) are based on an EMA centerline and ATR band widths.

![image](chart.png)

```csharp
// usage
IEnumerable<KeltnerResult> results = Indicator.GetKeltner(history, emaPeriod, multiplier, atrPeriod);  
```

## Parameters

| name | type | notes
| -- |-- |--
| `history` | IEnumerable\<[Quote](../../docs/GUIDE.md#quote)\> | Historical Quotes data should be at any consistent frequency (day, hour, minute, etc).  You must supply at least 2×`N` or `N`+100 periods of `history`, whichever is more.  Since this uses a smoothing technique, we recommend you use at least `N`+250 data points prior to the intended usage date for maximum precision.
| `emaPeriod` | int | Number of lookback periods (`E`) for the center line moving average.  Must be greater than 1 to calculate; however we suggest a larger period for an appropriate sample size.  Default is 20.
| `multiplier` | decimal | ATR Multiplier. Must be greater than 0.  Default is 2.
| `atrPeriod` | int | Number of lookback periods (`A`) for the Average True Range.  Must be greater than 1 to calculate; however we suggest a larger period for an appropriate sample size.  Default is 10.

Note: `N` is the greater of `E` or `A` periods.

## Response

```csharp
IEnumerable<KeltnerResult>
```

The first `N-1` periods will have `null` values since there's not enough data to calculate.  We always return the same number of elements as there are in the historical quotes.

### KeltnerResult

| name | type | notes
| -- |-- |--
| `Date` | DateTime | Date
| `UpperBand` | decimal | Upper band of Keltner Channel
| `Centerline` | decimal | EMA of Close price
| `LowerBand` | decimal | Lower band of Keltner Channel
| `Width` | decimal | Width as percent of Centerline price.  `(UpperBand-LowerBand)/Centerline`

## Example

```csharp
// fetch historical quotes from your favorite feed, in Quote format
IEnumerable<Quote> history = GetHistoryFromFeed("SPY");

// calculate Keltner(20)
IEnumerable<KeltnerResult> results = Indicator.GetKeltner(history,20,2.0,10);

// use results as needed
DateTime evalDate = DateTime.Parse("12/31/2018");
KeltnerResult result = results.Where(x=>x.Date==evalDate).FirstOrDefault();
Console.WriteLine("Upper Keltner Channel on {0} was ${1}", result.Date, result.UpperBand);
```

```bash
Upper Keltner Channel on 12/31/2018 was $262.19
```
