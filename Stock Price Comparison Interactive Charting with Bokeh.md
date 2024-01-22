### I. Scrape financial data from Yahoo Finance


```python
# import libraries
import yfinance as yf
import pandas as pd
import datetime
```


```python
# Download all stock price historical data from yahoo finance
spy_data = yf.download("SPY")
aapl_data = yf.download("AAPL")
goog_data = yf.download("GOOG")
tsla_data = yf.download("TSLA")
amzn_data = yf.download("AMZN")
meta_data = yf.download("META")
nvda_data = yf.download("NVDA")
```

    [*********************100%%**********************]  1 of 1 completed
    [*********************100%%**********************]  1 of 1 completed
    [*********************100%%**********************]  1 of 1 completed
    [*********************100%%**********************]  1 of 1 completed
    [*********************100%%**********************]  1 of 1 completed
    [*********************100%%**********************]  1 of 1 completed
    [*********************100%%**********************]  1 of 1 completed
    


```python
# Reset index to turn date to a separate column
spy_data.reset_index(inplace=True)
aapl_data.reset_index(inplace=True)
goog_data.reset_index(inplace=True)
tsla_data.reset_index(inplace=True)
amzn_data.reset_index(inplace=True)
meta_data.reset_index(inplace=True)
nvda_data.reset_index(inplace=True)
```


```python
# Add a column for ticker symbol
spy_data['Ticker']  = 'SPY'
aapl_data['Ticker'] = 'AAPL'
goog_data['Ticker'] = 'GOOG'
tsla_data['Ticker'] = 'TSLA'
amzn_data['Ticker'] = 'AMZN'
meta_data['Ticker'] = 'META'
nvda_data['Ticker'] = 'NVDA'
```


```python
# Preview dataframe
spy_data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
      <th>Ticker</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1993-01-29</td>
      <td>43.96875</td>
      <td>43.96875</td>
      <td>43.75000</td>
      <td>43.93750</td>
      <td>24.840672</td>
      <td>1003200</td>
      <td>SPY</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1993-02-01</td>
      <td>43.96875</td>
      <td>44.25000</td>
      <td>43.96875</td>
      <td>44.25000</td>
      <td>25.017361</td>
      <td>480500</td>
      <td>SPY</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1993-02-02</td>
      <td>44.21875</td>
      <td>44.37500</td>
      <td>44.12500</td>
      <td>44.34375</td>
      <td>25.070360</td>
      <td>201300</td>
      <td>SPY</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1993-02-03</td>
      <td>44.40625</td>
      <td>44.84375</td>
      <td>44.37500</td>
      <td>44.81250</td>
      <td>25.335360</td>
      <td>529400</td>
      <td>SPY</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1993-02-04</td>
      <td>44.96875</td>
      <td>45.09375</td>
      <td>44.46875</td>
      <td>45.00000</td>
      <td>25.441378</td>
      <td>531500</td>
      <td>SPY</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Put all stocks into a consolidated data frame
stock_all = pd.concat([spy_data, aapl_data, goog_data, tsla_data, amzn_data, meta_data, nvda_data])
```


```python
# Restrict data to work with to only dates after specific year
stock_all['Date'] = pd.to_datetime(stock_all['Date'])
df = stock_all[stock_all['Date'] >= '2010-01-01']
```


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 24024 entries, 4264 to 6288
    Data columns (total 8 columns):
     #   Column     Non-Null Count  Dtype         
    ---  ------     --------------  -----         
     0   Date       24024 non-null  datetime64[ns]
     1   Open       24024 non-null  float64       
     2   High       24024 non-null  float64       
     3   Low        24024 non-null  float64       
     4   Close      24024 non-null  float64       
     5   Adj Close  24024 non-null  float64       
     6   Volume     24024 non-null  int64         
     7   Ticker     24024 non-null  object        
    dtypes: datetime64[ns](1), float64(5), int64(1), object(1)
    memory usage: 1.6+ MB
    

### II. DATA VISUALIZATION USING BOKEH


```python
from bokeh.layouts import layout, column, row
from bokeh.models import ColumnDataSource, RangeSlider, DateRangeSlider, HoverTool, CDSView, BooleanFilter, CustomJS, Dropdown, Select, Div
from bokeh.plotting import figure, output_notebook, show
output_notebook()
```


<style>
        .bk-notebook-logo {
            display: block;
            width: 20px;
            height: 20px;
            background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABQAAAAUCAYAAACNiR0NAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAABx0RVh0U29mdHdhcmUAQWRvYmUgRmlyZXdvcmtzIENTNui8sowAAAOkSURBVDiNjZRtaJVlGMd/1/08zzln5zjP1LWcU9N0NkN8m2CYjpgQYQXqSs0I84OLIC0hkEKoPtiH3gmKoiJDU7QpLgoLjLIQCpEsNJ1vqUOdO7ppbuec5+V+rj4ctwzd8IIbbi6u+8f1539dt3A78eXC7QizUF7gyV1fD1Yqg4JWz84yffhm0qkFqBogB9rM8tZdtwVsPUhWhGcFJngGeWrPzHm5oaMmkfEg1usvLFyc8jLRqDOMru7AyC8saQr7GG7f5fvDeH7Ej8CM66nIF+8yngt6HWaKh7k49Soy9nXurCi1o3qUbS3zWfrYeQDTB/Qj6kX6Ybhw4B+bOYoLKCC9H3Nu/leUTZ1JdRWkkn2ldcCamzrcf47KKXdAJllSlxAOkRgyHsGC/zRday5Qld9DyoM4/q/rUoy/CXh3jzOu3bHUVZeU+DEn8FInkPBFlu3+nW3Nw0mk6vCDiWg8CeJaxEwuHS3+z5RgY+YBR6V1Z1nxSOfoaPa4LASWxxdNp+VWTk7+4vzaou8v8PN+xo+KY2xsw6une2frhw05CTYOmQvsEhjhWjn0bmXPjpE1+kplmmkP3suftwTubK9Vq22qKmrBhpY4jvd5afdRA3wGjFAgcnTK2s4hY0/GPNIb0nErGMCRxWOOX64Z8RAC4oCXdklmEvcL8o0BfkNK4lUg9HTl+oPlQxdNo3Mg4Nv175e/1LDGzZen30MEjRUtmXSfiTVu1kK8W4txyV6BMKlbgk3lMwYCiusNy9fVfvvwMxv8Ynl6vxoByANLTWplvuj/nF9m2+PDtt1eiHPBr1oIfhCChQMBw6Aw0UulqTKZdfVvfG7VcfIqLG9bcldL/+pdWTLxLUy8Qq38heUIjh4XlzZxzQm19lLFlr8vdQ97rjZVOLf8nclzckbcD4wxXMidpX30sFd37Fv/GtwwhzhxGVAprjbg0gCAEeIgwCZyTV2Z1REEW8O4py0wsjeloKoMr6iCY6dP92H6Vw/oTyICIthibxjm/DfN9lVz8IqtqKYLUXfoKVMVQVVJOElGjrnnUt9T9wbgp8AyYKaGlqingHZU/uG2NTZSVqwHQTWkx9hxjkpWDaCg6Ckj5qebgBVbT3V3NNXMSiWSDdGV3hrtzla7J+duwPOToIg42ChPQOQjspnSlp1V+Gjdged7+8UN5CRAV7a5EdFNwCjEaBR27b3W890TE7g24NAP/mMDXRWrGoFPQI9ls/MWO2dWFAar/xcOIImbbpA3zgAAAABJRU5ErkJggg==);
        }
    </style>
    <div>
        <a href="https://bokeh.org" target="_blank" class="bk-notebook-logo"></a>
        <span id="b2f884ce-0ff1-407b-99b0-bf2905e09305">Loading BokehJS ...</span>
    </div>






```python
# Define date range for graph
start_date = pd.to_datetime('2010-01-01')
end_date   = datetime.datetime.now()
date_rangeX = pd.date_range(start = start_date, end = end_date)

# Create lists for ticker & color
ticker_list = ['SPY',       'AAPL',    'AMZN',     'GOOG',   'META',       'NVDA',  'TSLA']
color_list  = ['lightblue', 'red',   'orange', 'darkblue',  'olive',  'darkgreen',  'pink']

# Instantiating the figure object
graph = figure(title = f"Stock Closing Prices {start_date.year}-{end_date.year}",
               x_axis_type = "datetime",
               x_axis_label = 'Date', 
               y_axis_label = 'Price (USD)',
               sizing_mode = "stretch_width",
               height = 500)

# Make font size of title bigger
graph.title.text_font_size = '20pt'
graph.title.align = "center"


# Draw graphs for all ticker on the list
for data, name, color in zip([df[df['Ticker']== 'SPY'],    df[df['Ticker']== 'AAPL'], 
                              df[df['Ticker']== 'AMZN'],   df[df['Ticker']== 'GOOG'], 
                              df[df['Ticker']== 'META'],   df[df['Ticker']== 'NVDA'], 
                              df[df['Ticker']== 'TSLA']], 
                              ticker_list, 
                              color_list ):
    graph.line(data['Date'], data['Adj Close'], line_width=1.5, color=color, alpha=0.8, legend_label=name)
    
# Put legend to top left corner & define click_policy
graph.legend.location = "top_left"
graph.legend.click_policy = "hide"

# Set up RangeSlider for timeline
timeline_slider = DateRangeSlider(    title = "Adjust timeline (x-axis) range",
                                      start = date_rangeX.min(),
                                      end   = date_rangeX.max(),
                                      step  = 1,
                                      value = (date_rangeX.min(), date_rangeX.max()),
                                      sizing_mode = "stretch_width")

# Add callback code
timeline_slider.js_link("value", graph.x_range, "start", attr_selector=0)
timeline_slider.js_link("value", graph.x_range, "end", attr_selector=1)

# Define the tooltip
hover_tool = HoverTool(  tooltips   = [   ('Date', '@x{%F}'),  ('Adj Price',  '@y{$0.00}' )   ],
                         formatters = {'@x': 'datetime'}  )

# Add hover_tool to graph
graph.add_tools(hover_tool)

# create layout
layout = column(graph, timeline_slider, sizing_mode = 'stretch_both')

# show result
show(layout)
```



<div id="a157b11a-8494-44ff-9164-ef616707c43b" data-root-id="p1117" style="display: contents;"></div>




