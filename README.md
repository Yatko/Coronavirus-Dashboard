# Coronavirus-Dashboard
Coronavirus Tracker Script

Coronavirus Tracker HTML/JS example: [coronavirus dashboard (CodePen)](https://codepen.io/_yatko/pen/KKdqBwv)

- **coronavirus-dashboard.html**
```html
<!DOCTYPE html>
<html lang=en>

<head>
    <title>Coronavirus App Example</title>
    <link href="https://quarantine.country/public/assets/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://quarantine.country/public/assets/css/covid-19-demo.css" rel="stylesheet">
</head>

<body>
    <div class="container mt-4 pt-4">

        <div class="row">
            <div class="col-10 offset-1">

                <div class="card">
                    <div class="card-header">
                        COVID-19 App Demo
                    </div>
                    <div class="card-body">
                        <h2 class="display-6">Coronavirus Cases in <span data-var-placeholder="country"></span></h2>
                        <p class="small text-muted">LIVE UPDATE</p>
                        <div class="data">

                            <!--total cases-->
                            <div class="d-inline qc_data-cases qc_data-cases-all">
                                <button class="btn btn-danger fixwidth">
                                    <strong data-country-placeholder="total_cases">0</strong>
                                </button>
                            </div>
                            <div class="d-inline qc_data-cases qc_data-cases-text pl-1 pl-sm-2">
                                <span class="text-muted small">Confirmed cases in the
									<span data-var-placeholder="country"></span>
                                </span>
                            </div>
                            <hr />

                            <!--active-->
                            <div class="d-inline qc_data-cases qc_data-cases-all">
                                <button class="btn btn-warning fixwidth">
                                    <strong data-country-placeholder="active_cases">0</strong>
                                </button>
                            </div>
                            <div class="d-inline qc_data-cases qc_data-cases-text pl-1 pl-sm-2">
                                <span class="text-muted small">Active cases in the 
									<span data-var-placeholder="country"></span>
                                </span>
                            </div>
                            <hr />

                            <!--recovered-->
                            <div class="d-inline qc_data-cases qc_data-cases-all">
                                <button class="btn btn-success fixwidth">
                                    <strong data-country-placeholder="recovered">0</strong>
                                </button>
                            </div>
                            <div class="d-inline qc_data-cases qc_data-cases-text pl-1 pl-sm-2">
                                <span class="text-muted small">Recoveding cases in the 
									<span data-var-placeholder="country"></span>
                                </span>
                            </div>
                            <hr />

                            <!--deaths-->
                            <div class="d-inline qc_data-cases qc_data-cases-all">
                                <button class="btn btn-secondary fixwidth">
                                    <strong data-country-placeholder="deaths">0</strong>
                                </button>
                            </div>
                            <div class="d-inline qc_data-cases qc_data-cases-text pl-1 pl-sm-2">
                                <span class="text-muted small">Death cases in the 
									<span data-var-placeholder="country"></span>
                                </span>
                            </div>
                            <hr />

                        </div>
                    </div>
                    <div class="card-footer text-muted small">
                        &#10095; More demos at <a class="text-muted" href="https://quarantine.country">Coronavirus Dashboard</a> &#9881; Build even more with <a class="text-muted" href="https://github.com/Yatko/Coronavirus-API">Coronavirus API</a>
                    </div>
                </div>
                <!--/card-->

            </div>
        </div>

    </div>
    <script src="tracker.js" type="text/javascript"></script>
</body>

</html>

```

- **tracker.js**
```javascript
/* 
Available Spots and Summary:
Total Cases, Active Cases, Deaths, Recovered, Tested, Critical, Change Ratio %, Summary
Historical data:
Day, Week, Month, Year, Change per Day, Difference, Summary
Regions:
World, Regions and Countries

Read documentation for more functionality - https://rapidapi.com/Yatko/api/coronavirus-live
See all available countries - https://api.quarantine.country/api/v1/summary/latest
*/

var countryFeedKey = 'usa';	//try china, spain, italy, etc.
var countryName = 'USA';		//try 中国, España, Italia, etc.

function ready(cb) {
  if( document.readyState !== 'loading' ) {
    cb();
  } else {
    document.addEventListener('DOMContentLoaded', function () {
        cb();
    });
  }
}

function fetchData(url) {
  return fetch(url)
    .then(function(response) {
      if(response.ok) {
        return response.json();
      }
    })
    .then(function(payload) {
      return payload['data'] || {};
    });
}

function formatNumber(number, precision, separate, separator, comma) {
  if(!number) {
    return '';
  }

    var re = '\\d(?=(\\d{' + (separate || 3) + '})+' + (precision > 0 ? '\\D' : '$') + ')',
        num = number.toFixed(Math.max(0, ~~precision));

    return (coma ? num.replace('.', comma) : num).replace(new RegExp(re, 'g'), '$&' + (separator || ','));
};

function fillPlaceholders(data) {
  var i;
  var varEl = document.querySelectorAll('[data-var-placeholder]');

  for(i = 0; i < varEl.length; i++) {
    var placeholder = varEl[i].getAttribute('data-var-placeholder');

    if(placeholder && placeholder != '') {
      switch(placeholder) {
        case 'country':
          varEl[i].innerText = countryName;    
          break;
      }
    }
  }

  var countryPlaceholderEl = document.querySelectorAll('[data-country-placeholder]');

  for(i = 0; i < countryPlaceholderEl.length; i++) {
    var placeholder = countryPlaceholderEl[i].getAttribute('data-country-placeholder');

    if(placeholder && placeholder != '' && data['summary'][placeholder]) {
      countryPlaceholderEl[i].innerText = parseInt(data['summary'][placeholder]).toLocaleString();
    }
  }
}

ready(
  function() {
    var url = 'https://api.quarantine.country/api/v1/summary/region?region=' + countryFeedKey;

    fetchData(url)
        .then(fillPlaceholders);

    setInterval(
      function() {
        fetchData(url)
          .then(fillPlaceholders);
      },
      10000
    );
  }
);

```

- [coronavirus dashboard (demo)](https://quarantine.country/coronavirus/)
- [coronavirus API](https://quarantine.country/coronavirus/api/), [documentation](https://quarantine.country/coronavirus/api/documentation)
