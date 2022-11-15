You can use the National Bank of Poland's (NBP) web API to get information about the current and archived exchange rates for currencies in tables A, B, and C.

# Quickstart

You can use any API testing tool (such as [Postman](postman.com) to try out the request and obtain the information you requested in response. Alternatively, you can also paste your request into the address bar of your browser.

# Query parameters

| Query parameter | Description |
| --- | --- |
| `table` | This is the table type corresponding to the table from which the average exchange rate is acquired.  <br>Tables [A](https://www.nbp.pl/home.aspx?f=/kursy/kursya.html) and [B](https://www.nbp.pl/home.aspx?f=/kursy/kursyb.html) contain information about different currencies, while table [C](https://www.nbp.pl/home.aspx?f=/kursy/kursyc.html) is used to obtain information that includes buying and selling prices. |
| `code` | This is the three-letter currency code based on the ISO-4217 standard. |
| `topCount` | The maximum size of the returned data series. |
| `date`, `startDate`, `endDate` | Date parameters based on the ISO-8601 standard (YYYY-MM-DD); can be used to obtain the average exchange rates from a particular date or timespan. Note that the query cannot cover time longer than 93 days. |

**Information about update times**

- The data update in table A occurs every day between 11:45 am and 12:15 pm.
- The data update in table B occurs every Wednesday (or the day before if Wednesday is a bank holiday) between 11:45 am and 12:15 pm.
- The data update in table C occurs every day between 7:45 am and 8:15 am.
    

# Response Parameters

You can configure the response format to either json or xml by adding `?format=json` or '?format=xml' to your request. By default, json formatting is used.

Example request for querying the last ten average exchange rates for Euro in json format:

``` plaintext
http://api.nbp.pl/api/exchangerates/rates/a/eur/last/10/?format=json

```

| Response Parameter | Description |
| --- | --- |
| `table` | This is the table type corresponding to the table from which the average exchange rate is acquired.  <br>Tables [A](https://www.nbp.pl/home.aspx?f=/kursy/kursya.html) and [B](https://www.nbp.pl/home.aspx?f=/kursy/kursyb.html) contain information about different currencies, while table [C](https://www.nbp.pl/home.aspx?f=/kursy/kursyc.html) is used to obtain information that includes buying and selling prices. |
| `no` | This is the internal number assigned to every table. |
| `TradingDate` | _Table C only_ This is the trading date. |
| `effectiveDate` | The date when the exchange rates were published. |
| `Rates` | This is a list of exchange rates of currencies in a particular table. |
| `Country` | This is the name of the country associated with a particular currency. |
| `Symbol` | _Archived exchange rates only_ This is the currency symbol. |
| `Currency` | This is the currency name. |
| `Code` | This is the three-letter currency code based on the ISO-4217 standard. |
| `Bid` | _Table C only_ This is the calculated buying price of a particular currency. |
| `Ask` | _Table C only_ This is the calculated selling price of a particular currency. |
| `Mid` | _Table A and B only_ This is the calculated average exchange rate of a particular currency. |

# Sample Requests and Responses
## http://api.nbp.pl/api/exchangerates/rates/{table}/{code}/

`http://api.nbp.pl/api/exchangerates/rates/a/eur/` This request provides the current average exchange rate for Euro.

```
{
    "table": "A",
    "currency": "euro",
    "code": "EUR",
    "rates": [
        {
            "no": "220/A/NBP/2022",
            "effectiveDate": "2022-11-15",
            "mid": 4.6985
        }
    ]
}
```

`http://api.nbp.pl/api/exchangerates/rates/c/eur/` This request provides the current buying and selling prices of Euro.

```
{
    "table": "C",
    "currency": "euro",
    "code": "EUR",
    "rates": [
        {
            "no": "220/C/NBP/2022",
            "effectiveDate": "2022-11-15",
            "bid": 4.658,
            "ask": 4.7522
        }
    ]
}
```
## http://api.nbp.pl/api/exchangerates/rates/{table}/{code}/{startDate}/{endDate}/

`http://api.nbp.pl/api/exchangerates/rates/c/eur/2022-11-08/2022-11-15/` This request provides an overview of how Euro buying and selling prices changed between the 8th and 15th of November.

```
{
    "table": "C",
    "currency": "euro",
    "code": "EUR",
    "rates": [
        {
            "no": "216/C/NBP/2022",
            "effectiveDate": "2022-11-08",
            "bid": 4.6372,
            "ask": 4.7308
        },
        {
            "no": "217/C/NBP/2022",
            "effectiveDate": "2022-11-09",
            "bid": 4.6423,
            "ask": 4.7361
        },
        {
            "no": "218/C/NBP/2022",
            "effectiveDate": "2022-11-10",
            "bid": 4.6587,
            "ask": 4.7529
        },
        {
            "no": "219/C/NBP/2022",
            "effectiveDate": "2022-11-14",
            "bid": 4.6393,
            "ask": 4.7331
        },
        {
            "no": "220/C/NBP/2022",
            "effectiveDate": "2022-11-15",
            "bid": 4.658,
            "ask": 4.7522
        }
    ]
}
```
## http://api.nbp.pl/api/exchangerates/rates/{table}/{code}/last/{topCount}/

`http://api.nbp.pl/api/exchangerates/rates/a/eur/last/3/` This request provides the three most recent average exchange rates for Euro.


```
{
    "table": "A",
    "currency": "euro",
    "code": "EUR",
    "rates": [
        {
            "no": "218/A/NBP/2022",
            "effectiveDate": "2022-11-10",
            "mid": 4.7146
        },
        {
            "no": "219/A/NBP/2022",
            "effectiveDate": "2022-11-14",
            "mid": 4.6794
        },
        {
            "no": "220/A/NBP/2022",
            "effectiveDate": "2022-11-15",
            "mid": 4.6985
        }
    ]
}
```
