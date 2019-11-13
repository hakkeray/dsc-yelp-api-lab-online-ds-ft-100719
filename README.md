
# Yelp API - Lab


## Introduction 

We've seen how the Yelp API works and how to create basic visualizations using Folium. It's time to put those skills to work in order to create a working map! Taking things a step further, you'll also independently explore how to perform pagination in order to retrieve a full results set from the Yelp API!

## Objectives

You will be able to: 

* Using pagination and multiple functions, gather large amounts of data from an API, parse the data and make sense of it with meaningful analysis
* Create maps using Folium

## Problem Introduction

You've now worked with some API calls, but we have yet to see how to retrieve a more complete dataset in a programmatic manner. Returning to the Yelp API, the [documentation](https://www.yelp.com/developers/documentation/v3/business_search) also provides us details regarding the API limits. These often include details about the number of requests a user is allowed to make within a specified time limit and the maximum number of results to be returned. In this case, we are told that any request has a maximum of 50 results per request and defaults to 20. Furthermore, any search will be limited to a total of 1000 results. To retrieve all 1000 of these results, we would have to page through the results piece by piece, retrieving 50 at a time. Processes such as these are often referred to as pagination.

In this lab, you will define a search and then paginate over the results to retrieve all of the results. You'll then parse these responses as a DataFrame (for further exploration) and create a map using Folium to visualize the results geographically.

## Part I - Make the Initial Request

Start by making an initial request to the Yelp API. Your search must include at least 2 parameters: **term** and **location**. For example, you might search for pizza restaurants in NYC. The term and location is up to you but make the request below.


```python
import json

#Our previous function for loading our api key file
def get_keys(path):
    with open(path) as f:
        return json.load(f)
```


```python
keys = get_keys("/Users/hakkeray/datascience/mod2/sec12/dsc-yelp-api-lab-online-ds-ft-100719/.secret/yelp_api.json")

api_key = keys['api_key']
```


```python
import requests
url = 'https://api.yelp.com/v3/businesses/search'

headers = {
        'Authorization': 'Bearer {}'.format(api_key),
    }

url_params = {
                'location': 'LA',
                'term' : 'tacos',
                'limit' : 50,
                'price' : "1,2,3,4",
                'open_now' : True
            }
r = requests.get(url, headers=headers, params=url_params)
print(r.text)
```

    {"businesses": [{"id": "QKovUc1TmSNtZh0j5ZEagw", "alias": "leos-tacos-truck-los-angeles", "name": "Leo's Tacos Truck", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/EnENQLNGtYeRoN1BXM_tBw/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/leos-tacos-truck-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 1770, "categories": [{"alias": "foodtrucks", "title": "Food Trucks"}, {"alias": "tacos", "title": "Tacos"}], "rating": 4.5, "coordinates": {"latitude": 34.046438, "longitude": -118.345718}, "transactions": [], "price": "$", "location": {"address1": "1515 S La Brea Ave", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90019", "country": "US", "state": "CA", "display_address": ["1515 S La Brea Ave", "Los Angeles, CA 90019"]}, "phone": "+13233462001", "display_phone": "(323) 346-2001", "distance": 2804.549874934235}, {"id": "UUlGlPfy0FsReQ2LWWBSWw", "alias": "tire-shop-taqueria-los-angeles", "name": "Tire Shop Taqueria", "image_url": "https://s3-media4.fl.yelpcdn.com/bphoto/bYCXExdvf9S94Ssmk5xpIA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tire-shop-taqueria-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 433, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0097381464074, "longitude": -118.265285901725}, "transactions": [], "price": "$", "location": {"address1": "4069 S Avalon Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90011", "country": "US", "state": "CA", "display_address": ["4069 S Avalon Blvd", "Los Angeles, CA 90011"]}, "phone": "+13232352812", "display_phone": "(323) 235-2812", "distance": 7743.367507855298}, {"id": "VqG_rccDoUXreiaHH0DEUA", "alias": "guisados-dtla-los-angeles-2", "name": "Guisados DTLA", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/n_BRYnN668g33o1uFUDdYg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/guisados-dtla-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 1724, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0465305975394, "longitude": -118.250679851854}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "541 S Spring St", "address2": "Ste 101", "address3": "", "city": "Los Angeles", "zip_code": "90013", "country": "US", "state": "CA", "display_address": ["541 S Spring St", "Ste 101", "Los Angeles, CA 90013"]}, "phone": "+12136277656", "display_phone": "(213) 627-7656", "distance": 6725.171634158761}, {"id": "Ti2Ksp2oPj6rpdp2tQcaVA", "alias": "sonoratown-los-angeles", "name": "Sonoratown", "image_url": "https://s3-media4.fl.yelpcdn.com/bphoto/wOFFBVqWSzMfwx-vDbWPUw/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/sonoratown-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 814, "categories": [{"alias": "tacos", "title": "Tacos"}], "rating": 4.5, "coordinates": {"latitude": 34.0416441, "longitude": -118.2522657}, "transactions": [], "price": "$", "location": {"address1": "208 E 8th St", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90014", "country": "US", "state": "CA", "display_address": ["208 E 8th St", "Los Angeles, CA 90014"]}, "phone": "+12136283710", "display_phone": "(213) 628-3710", "distance": 6738.523417192994}, {"id": "qpcH_Fub586U2kw_pQJS3w", "alias": "guisados-los-angeles", "name": "Guisados", "image_url": "https://s3-media4.fl.yelpcdn.com/bphoto/XgalCUyhs_6f9uMok2n78Q/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/guisados-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 2413, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "latin", "title": "Latin American"}], "rating": 4.5, "coordinates": {"latitude": 34.04812, "longitude": -118.21147}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "2100 East Cesar E Chavez Ave", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90033", "country": "US", "state": "CA", "display_address": ["2100 East Cesar E Chavez Ave", "Los Angeles, CA 90033"]}, "phone": "+13232647201", "display_phone": "(323) 264-7201", "distance": 10234.474965137326}, {"id": "BDRVlHnK4l0T0ANb7M-Eqg", "alias": "guisados-los-angeles-3", "name": "Guisados", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/UwbMeqHMrBZBjix9pevSBw/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/guisados-los-angeles-3?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 2017, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0702395145121, "longitude": -118.250448424643}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "1261 W Sunset Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90026", "country": "US", "state": "CA", "display_address": ["1261 W Sunset Blvd", "Los Angeles, CA 90026"]}, "phone": "+12132507600", "display_phone": "(213) 250-7600", "distance": 6604.197846892978}, {"id": "SNlf3YpKkDG-e4R9jkwLhA", "alias": "guerrilla-tacos-los-angeles", "name": "Guerrilla Tacos", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/71DQapP2Fafn-8Kck2IVww/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/guerrilla-tacos-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 676, "categories": [{"alias": "tacos", "title": "Tacos"}, {"alias": "cocktailbars", "title": "Cocktail Bars"}, {"alias": "venues", "title": "Venues & Event Spaces"}], "rating": 4.0, "coordinates": {"latitude": 34.0343720116648, "longitude": -118.232130253186}, "transactions": [], "price": "$$", "location": {"address1": "2000 E 7th St", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90021", "country": "US", "state": "CA", "display_address": ["2000 E 7th St", "Los Angeles, CA 90021"]}, "phone": "+12133753300", "display_phone": "(213) 375-3300", "distance": 8762.12814141014}, {"id": "9oQnnXSTo8i2TliFPktxLg", "alias": "sonoritas-prime-tacos-los-angeles", "name": "Sonoritas Prime Tacos", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/VH_w93WSWOIRAFrEqStf-Q/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/sonoritas-prime-tacos-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 764, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.04093, "longitude": -118.44328}, "transactions": ["pickup", "delivery"], "price": "$$", "location": {"address1": "2004 Sawtelle Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90025", "country": "US", "state": "CA", "display_address": ["2004 Sawtelle Blvd", "Los Angeles, CA 90025"]}, "phone": "+13104449100", "display_phone": "(310) 444-9100", "distance": 11466.19898658976}, {"id": "omaJSGvnj2vaIJ_MBxeyBw", "alias": "l-a-birria-los-angeles", "name": "L.A. Birria", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/OQwuJCYJxPSBwvrsu3RzWg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/l-a-birria-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 101, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "foodtrucks", "title": "Food Trucks"}], "rating": 4.5, "coordinates": {"latitude": 34.0322509474725, "longitude": -118.334643803537}, "transactions": [], "price": "$", "location": {"address1": "4380 W Adams Blvd", "address2": "", "address3": null, "city": "Los Angeles", "zip_code": "90018", "country": "US", "state": "CA", "display_address": ["4380 W Adams Blvd", "Los Angeles, CA 90018"]}, "phone": "+13237231840", "display_phone": "(323) 723-1840", "distance": 3483.6546554124593}, {"id": "520Wd4FU_DixBprFQQnnxg", "alias": "tacos-tumbras-a-tomas-los-angeles", "name": "Tacos Tumbras a Tomas", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/QpT4L7xD7zvtUIUNzLscjA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tacos-tumbras-a-tomas-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 991, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0507793579027, "longitude": -118.249036073685}, "transactions": [], "price": "$", "location": {"address1": "Grand Central Market Space A-5", "address2": "", "address3": "317 S Broadway", "city": "Los Angeles", "zip_code": "90013", "country": "US", "state": "CA", "display_address": ["Grand Central Market Space A-5", "317 S Broadway", "Los Angeles, CA 90013"]}, "phone": "+12136201071", "display_phone": "(213) 620-1071", "distance": 6772.175508362991}, {"id": "oNHjZ3wC0F9l5KpuqHd45w", "alias": "rickys-fish-tacos-los-angeles-3", "name": "Ricky's Fish Tacos", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/xOUSwBSssYYBgbLzqV_2dg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/rickys-fish-tacos-los-angeles-3?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 1161, "categories": [{"alias": "foodtrucks", "title": "Food Trucks"}, {"alias": "tacos", "title": "Tacos"}], "rating": 4.5, "coordinates": {"latitude": 34.112686, "longitude": -118.267215}, "transactions": [], "price": "$", "location": {"address1": "3061 Riverside Dr", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90027", "country": "US", "state": "CA", "display_address": ["3061 Riverside Dr", "Los Angeles, CA 90027"]}, "phone": "+13233956233", "display_phone": "(323) 395-6233", "distance": 7551.273022233048}, {"id": "eoSG9gouwkL5M35KN-qB9w", "alias": "calle-tacos-los-angeles", "name": "Calle Tacos", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/JwVALEoAMR1FvHbZyhz_vQ/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/calle-tacos-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 772, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.1014551, "longitude": -118.3314412}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "6508 Hollywood Blvd", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90028", "country": "US", "state": "CA", "display_address": ["6508 Hollywood Blvd", "Los Angeles, CA 90028"]}, "phone": "+13234659100", "display_phone": "(323) 465-9100", "distance": 4523.13965421703}, {"id": "Osce6shPInsQLAhtJEJnng", "alias": "el-zarape-los-angeles-2", "name": "El Zarape", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/6-MA_D8kelEyaEeqxNBW4Q/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/el-zarape-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 561, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.09544, "longitude": -118.30256}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "5203 Fountain Ave", "address2": "Ste B", "address3": "", "city": "Los Angeles", "zip_code": "90029", "country": "US", "state": "CA", "display_address": ["5203 Fountain Ave", "Ste B", "Los Angeles, CA 90029"]}, "phone": "+13237410465", "display_phone": "(323) 741-0465", "distance": 4121.884285978789}, {"id": "6Dd4kTDiikEB3wQp___0fA", "alias": "taqueria-los-anaya-los-angeles", "name": "Taqueria Los Anaya", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/T6KacOTiaa2TudDlU2lykQ/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/taqueria-los-anaya-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 1137, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0327911, "longitude": -118.3417282}, "transactions": [], "price": "$$", "location": {"address1": "4651 W Adams Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90016", "country": "US", "state": "CA", "display_address": ["4651 W Adams Blvd", "Los Angeles, CA 90016"]}, "phone": "+13237314070", "display_phone": "(323) 731-4070", "distance": 3720.848506858027}, {"id": "k3D-nW6EjjJguYkUupCUaA", "alias": "tacos-1986-los-angeles", "name": "Tacos 1986", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/1wlpvLSNpuuJZY0U403M3g/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tacos-1986-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 273, "categories": [{"alias": "tacos", "title": "Tacos"}, {"alias": "foodstands", "title": "Food Stands"}], "rating": 4.0, "coordinates": {"latitude": 34.0458186, "longitude": -118.2514334}, "transactions": [], "price": "$", "location": {"address1": "609 S Spring St", "address2": null, "address3": null, "city": "Los Angeles", "zip_code": "90014", "country": "US", "state": "CA", "display_address": ["609 S Spring St", "Los Angeles, CA 90014"]}, "phone": "+12139887202", "display_phone": "(213) 988-7202", "distance": 6678.33609472028}, {"id": "ra8QFgeKZYNRaYABohgHNg", "alias": "leos-tacos-truck-los-angeles-5", "name": "Leo's Tacos Truck", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/wPXqFxNi1KU3rGK-vudALQ/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/leos-tacos-truck-los-angeles-5?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 285, "categories": [{"alias": "foodtrucks", "title": "Food Trucks"}, {"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.0986671447754, "longitude": -118.30989074707}, "transactions": [], "price": "$", "location": {"address1": "5525 Sunset Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90028", "country": "US", "state": "CA", "display_address": ["5525 Sunset Blvd", "Los Angeles, CA 90028"]}, "phone": "+13233462001", "display_phone": "(323) 346-2001", "distance": 4252.1456108045095}, {"id": "K3msdd0tuGwuE2fm_W94MQ", "alias": "best-fish-taco-in-ensenada-los-angeles", "name": "Best Fish Taco In Ensenada", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/oEDBOqMt2u15VA3-61Z9Cg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/best-fish-taco-in-ensenada-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 2128, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.1010002, "longitude": -118.2872851}, "transactions": [], "price": "$", "location": {"address1": "1650 Hillhurst Ave", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90027", "country": "US", "state": "CA", "display_address": ["1650 Hillhurst Ave", "Los Angeles, CA 90027"]}, "phone": "+13234665552", "display_phone": "(323) 466-5552", "distance": 5401.805241378904}, {"id": "lhjyWpMMbysXxS-6ZJ9QNg", "alias": "tacos-tu-madre-los-angeles", "name": "Tacos Tu Madre", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/oqv9foKgF1sV3joKKqTVJw/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tacos-tu-madre-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 1108, "categories": [{"alias": "tapasmallplates", "title": "Tapas/Small Plates"}, {"alias": "mexican", "title": "Mexican"}, {"alias": "breakfast_brunch", "title": "Breakfast & Brunch"}], "rating": 3.5, "coordinates": {"latitude": 34.04738, "longitude": -118.435126}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "1945 1/2 Westwood Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90025", "country": "US", "state": "CA", "display_address": ["1945 1/2 Westwood Blvd", "Los Angeles, CA 90025"]}, "phone": "+14248327092", "display_phone": "(424) 832-7092", "distance": 10597.300569872106}, {"id": "F-rQ33zVrlNIPBFPoXZOSQ", "alias": "cactus-taqueria-1-los-angeles-3", "name": "Cactus Taqueria #1", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/Eu-qMXzzd6RH7jyyx_Vhgg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/cactus-taqueria-1-los-angeles-3?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 901, "categories": [{"alias": "tacos", "title": "Tacos"}], "rating": 4.0, "coordinates": {"latitude": 34.08827, "longitude": -118.32633}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "950 Vine St", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90038", "country": "US", "state": "CA", "display_address": ["950 Vine St", "Los Angeles, CA 90038"]}, "phone": "+13234645865", "display_phone": "(323) 464-5865", "distance": 3002.605137835769}, {"id": "T2yz7XPwL8CpBUcwnHhfow", "alias": "chuy-s-tacos-dorados-los-angeles-2", "name": "Chuy\u2019s Tacos Dorados", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/65eX5nS7hKIGjzwwKdl7Cg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/chuy-s-tacos-dorados-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 101, "categories": [{"alias": "tacos", "title": "Tacos"}, {"alias": "foodstands", "title": "Food Stands"}], "rating": 4.5, "coordinates": {"latitude": 34.0394174, "longitude": -118.231553}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "1335 Willow St", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90013", "country": "US", "state": "CA", "display_address": ["1335 Willow St", "Los Angeles, CA 90013"]}, "phone": "+13236866272", "display_phone": "(323) 686-6272", "distance": 8638.49842595994}, {"id": "Lop79P2KM9zFUCCaBYz6zA", "alias": "tlayuda-la-restaurant-los-angeles-3", "name": "Tlayuda La Restaurant", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/te_SqWzn5aRxYOWIoHqQWg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tlayuda-la-restaurant-los-angeles-3?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 701, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0906351, "longitude": -118.307928}, "transactions": ["pickup", "delivery"], "price": "$$", "location": {"address1": "5450 Santa Monica Blvd", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90029", "country": "US", "state": "CA", "display_address": ["5450 Santa Monica Blvd", "Los Angeles, CA 90029"]}, "phone": "+12132614667", "display_phone": "(213) 261-4667", "distance": 3458.2073511243375}, {"id": "xVgz_F3gnbhyn_2goJ8vYQ", "alias": "pinches-tacos-los-angeles", "name": "Pinches Tacos", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/IJGOshRYGlVYLMWh31Wyvg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/pinches-tacos-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 818, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.09772, "longitude": -118.36769}, "transactions": [], "price": "$$", "location": {"address1": "8200 W Sunset Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90046", "country": "US", "state": "CA", "display_address": ["8200 W Sunset Blvd", "Los Angeles, CA 90046"]}, "phone": "+13236500614", "display_phone": "(323) 650-0614", "distance": 5860.408350464527}, {"id": "fr7Sk_5f368QKt2-LWe_kA", "alias": "tacos-los-reyes-los-angeles", "name": "Tacos Los Reyes", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/-OpnlZMTCXsTqJAWQKwnPg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tacos-los-reyes-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 35, "categories": [{"alias": "foodtrucks", "title": "Food Trucks"}, {"alias": "tacos", "title": "Tacos"}], "rating": 4.5, "coordinates": {"latitude": 34.0525975258226, "longitude": -118.309236487059}, "transactions": [], "price": "$", "location": {"address1": "3300 W Olympic Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90019", "country": "US", "state": "CA", "display_address": ["3300 W Olympic Blvd", "Los Angeles, CA 90019"]}, "phone": "+13103079110", "display_phone": "(310) 307-9110", "distance": 1509.6578256091504}, {"id": "DoyIC0ElqglrlHBKDxXOwg", "alias": "sugar-taco-los-angeles-2", "name": "Sugar Taco", "image_url": "https://s3-media4.fl.yelpcdn.com/bphoto/TdclQCPY-BdXllqDH2oKiQ/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/sugar-taco-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 247, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "vegetarian", "title": "Vegetarian"}], "rating": 4.5, "coordinates": {"latitude": 34.08370683, "longitude": -118.34784433}, "transactions": ["pickup", "delivery"], "price": "$$", "location": {"address1": "7257 Melrose Ave", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90046", "country": "US", "state": "CA", "display_address": ["7257 Melrose Ave", "Los Angeles, CA 90046"]}, "phone": "+13105268004", "display_phone": "(310) 526-8004", "distance": 3454.350053290111}, {"id": "ul2dT8QzauKP5Yc-q_DZ_g", "alias": "homestate-los-angeles", "name": "HomeState", "image_url": "https://s3-media4.fl.yelpcdn.com/bphoto/UYPvVhILsv1TFeGZdxKmsg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/homestate-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 976, "categories": [{"alias": "tex-mex", "title": "Tex-Mex"}, {"alias": "breakfast_brunch", "title": "Breakfast & Brunch"}, {"alias": "tacos", "title": "Tacos"}], "rating": 4.5, "coordinates": {"latitude": 34.09979, "longitude": -118.29021}, "transactions": ["pickup"], "price": "$", "location": {"address1": "4624 Hollywood Blvd", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90027", "country": "US", "state": "CA", "display_address": ["4624 Hollywood Blvd", "Los Angeles, CA 90027"]}, "phone": "+13239061122", "display_phone": "(323) 906-1122", "distance": 5133.579631402432}, {"id": "TZFTVm9WbsWILRdY98SYtA", "alias": "tacos-el-compita-los-angeles-4", "name": "Tacos El Compita", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/jDrIM2mPNRuWOxWw0gmBjA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tacos-el-compita-los-angeles-4?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 290, "categories": [{"alias": "streetvendors", "title": "Street Vendors"}, {"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.0485400612365, "longitude": -118.332734741271}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "4477 W Pico Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90019", "country": "US", "state": "CA", "display_address": ["4477 W Pico Blvd", "Los Angeles, CA 90019"]}, "phone": "+13239350490", "display_phone": "(323) 935-0490", "distance": 1788.7459704428218}, {"id": "1OXJaygVHAEGooC5gsH_Pg", "alias": "la-esquina-los-angeles", "name": "La Esquina", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/IJuLvIUetQA6b0gjeuPWNQ/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/la-esquina-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 189, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0292220404164, "longitude": -118.389159701765}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "8905 Venice Blvd", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90034", "country": "US", "state": "CA", "display_address": ["8905 Venice Blvd", "Los Angeles, CA 90034"]}, "phone": "+14242586396", "display_phone": "(424) 258-6396", "distance": 7207.750444727187}, {"id": "zpCjv61s-5b-WJSd8opFvA", "alias": "candela-los-angeles", "name": "Candela", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/tse8Y2ZNJz5oOQjuB_wt3A/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/candela-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 648, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "lounges", "title": "Lounges"}, {"alias": "venues", "title": "Venues & Event Spaces"}], "rating": 4.0, "coordinates": {"latitude": 34.0596284, "longitude": -118.3451919}, "transactions": ["restaurant_reservation", "pickup"], "price": "$$", "location": {"address1": "831 S La Brea Ave", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90036", "country": "US", "state": "CA", "display_address": ["831 S La Brea Ave", "Los Angeles, CA 90036"]}, "phone": "+13239360533", "display_phone": "(323) 936-0533", "distance": 2224.3276207090194}, {"id": "m6H1NIGD6MnPF7i1HE5ekA", "alias": "l-a-autentica-birrieria-north-hollywood-2", "name": "L.A Autentica Birrieria", "image_url": "https://s3-media4.fl.yelpcdn.com/bphoto/IUFqCqC0OXqsk_e16jiJlg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/l-a-autentica-birrieria-north-hollywood-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 51, "categories": [{"alias": "tacos", "title": "Tacos"}, {"alias": "foodtrucks", "title": "Food Trucks"}], "rating": 5.0, "coordinates": {"latitude": 34.1871511, "longitude": -118.3729288}, "transactions": [], "price": "$", "location": {"address1": "11117 Victory Blvd", "address2": null, "address3": null, "city": "North Hollywood", "zip_code": "91606", "country": "US", "state": "CA", "display_address": ["11117 Victory Blvd", "North Hollywood, CA 91606"]}, "phone": "+13236900987", "display_phone": "(323) 690-0987", "distance": 14746.067220052508}, {"id": "mLQ8UtRY-NvMG-PR3jm-Yg", "alias": "plancha-tacos-los-angeles-2", "name": "Plancha Tacos", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/vPwqtmI4vOkiqr_ZWvsuhA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/plancha-tacos-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 702, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.072365, "longitude": -118.369731}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "8250 W 3rd St", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90048", "country": "US", "state": "CA", "display_address": ["8250 W 3rd St", "Los Angeles, CA 90048"]}, "phone": "+13239519911", "display_phone": "(323) 951-9911", "distance": 4612.005207371957}, {"id": "hcUxpi--BTuVNEMttimC7Q", "alias": "king-taco-los-angeles-12", "name": "King Taco", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/szD8jlVwfArk91RmsUyCdw/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/king-taco-los-angeles-12?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 1012, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 3.5, "coordinates": {"latitude": 34.04675, "longitude": -118.28247}, "transactions": [], "price": "$", "location": {"address1": "2020 W Pico Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90006", "country": "US", "state": "CA", "display_address": ["2020 W Pico Blvd", "Los Angeles, CA 90006"]}, "phone": "+12133848115", "display_phone": "(213) 384-8115", "distance": 3973.0888545086605}, {"id": "Bq6I52mOTyC2iDHU2rh_5w", "alias": "el-cartel-los-angeles", "name": "El Cartel", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/LIc6AkZKCCWsU5VOOHOpeA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/el-cartel-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 497, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.0625121510041, "longitude": -118.348814569312}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "5515 Wilshire Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90036", "country": "US", "state": "CA", "display_address": ["5515 Wilshire Blvd", "Los Angeles, CA 90036"]}, "phone": "+13239311281", "display_phone": "(323) 931-1281", "distance": 2529.2225545505703}, {"id": "ivnX_tNhngBvdepsQFfGIw", "alias": "tacos-los-poblanos-los-angeles-2", "name": "Tacos Los Poblanos", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/HaKLR9P-n8CRTVU3HIyCKQ/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tacos-los-poblanos-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 204, "categories": [{"alias": "foodtrucks", "title": "Food Trucks"}, {"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 33.9891021960076, "longitude": -118.265354705316}, "transactions": [], "price": "$", "location": {"address1": "5821 Avalon Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90011", "country": "US", "state": "CA", "display_address": ["5821 Avalon Blvd", "Los Angeles, CA 90011"]}, "phone": "+13237436639", "display_phone": "(323) 743-6639", "distance": 9572.12591289189}, {"id": "uA3TautyZ7bby0w8Scwufg", "alias": "bennys-tacos-and-chicken-rotisserie-los-angeles-4", "name": "Benny's Tacos & Chicken Rotisserie", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/DvYYgTEklvMM_HMhbMX1rA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/bennys-tacos-and-chicken-rotisserie-los-angeles-4?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 1985, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "tex-mex", "title": "Tex-Mex"}], "rating": 4.5, "coordinates": {"latitude": 33.95998, "longitude": -118.41685}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "7101 W Manchester Ave", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90045", "country": "US", "state": "CA", "display_address": ["7101 W Manchester Ave", "Los Angeles, CA 90045"]}, "phone": "+13106708226", "display_phone": "(310) 670-8226", "distance": 14320.8826728025}, {"id": "j63ipopHrhHHnoy9Ac5m0g", "alias": "tacos-delta-los-angeles", "name": "Tacos Delta", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/lpZwO94DcaBYRbdUtMEeEA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tacos-delta-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 566, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.0908699035645, "longitude": -118.27921295166}, "transactions": [], "price": "$", "location": {"address1": "3806 W Sunset Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90026", "country": "US", "state": "CA", "display_address": ["3806 W Sunset Blvd", "Los Angeles, CA 90026"]}, "phone": "+13236642848", "display_phone": "(323) 664-2848", "distance": 5067.850007867751}, {"id": "5o_XEyhLrgpB6Cmeqp9NGg", "alias": "casa-la-do\u00f1a-los-angeles-2", "name": "Casa La Do\u00f1a", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/suj2hhANQVOdhNBWSkQnsg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/casa-la-do%C3%B1a-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 629, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0426283329725, "longitude": -118.253294751048}, "transactions": [], "price": "$", "location": {"address1": "800 S Main St", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90014", "country": "US", "state": "CA", "display_address": ["800 S Main St", "Los Angeles, CA 90014"]}, "phone": "+12136277441", "display_phone": "(213) 627-7441", "distance": 6627.716588168547}, {"id": "_FYGof7wh6AsZW_L_wuV8g", "alias": "carnitas-michoacan-los-angeles-2", "name": "Carnitas Michoacan", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/g3NHNrMkha76xC8DFffu2A/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/carnitas-michoacan-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 1211, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.07251, "longitude": -118.2213}, "transactions": [], "price": "$", "location": {"address1": "1901 N Broadway", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90031", "country": "US", "state": "CA", "display_address": ["1901 N Broadway", "Los Angeles, CA 90031"]}, "phone": "+13232252729", "display_phone": "(323) 225-2729", "distance": 9305.266759797993}, {"id": "kIG3vzmjCR1jMuMEnB2zeg", "alias": "street-tacos-and-grill-los-angeles-2", "name": "Street Tacos and Grill", "image_url": "https://s3-media4.fl.yelpcdn.com/bphoto/Nc5depLSxmcArp_RAmOIug/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/street-tacos-and-grill-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 125, "categories": [{"alias": "tacos", "title": "Tacos"}], "rating": 5.0, "coordinates": {"latitude": 34.047026603305, "longitude": -118.218283063198}, "transactions": [], "price": "$", "location": {"address1": "1843 1/2 E 1st", "address2": null, "address3": "", "city": "Los Angeles", "zip_code": "90033", "country": "US", "state": "CA", "display_address": ["1843 1/2 E 1st", "Los Angeles, CA 90033"]}, "phone": "+13236857727", "display_phone": "(323) 685-7727", "distance": 9634.984983842598}, {"id": "HTvyn97PloIn1kIGTIj-2A", "alias": "mexicali-taco-and-co-los-angeles-2", "name": "Mexicali Taco & Co", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/wxrtV1P2Wgx-N-Xx1fcxkQ/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/mexicali-taco-and-co-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 877, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.0634262, "longitude": -118.2456717}, "transactions": [], "price": "$", "location": {"address1": "702 N Figueroa St", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90012", "country": "US", "state": "CA", "display_address": ["702 N Figueroa St", "Los Angeles, CA 90012"]}, "phone": "+12136130416", "display_phone": "(213) 613-0416", "distance": 6982.298567763298}, {"id": "_o6cTaR_pnm2XucZS6fTug", "alias": "guisados-west-hollywood", "name": "Guisados", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/UAnlaz1gAlADKnixEV8sGg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/guisados-west-hollywood?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 807, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0844498, "longitude": -118.3853226}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "8935 Santa Monica Blvd", "address2": "", "address3": "", "city": "West Hollywood", "zip_code": "90069", "country": "US", "state": "CA", "display_address": ["8935 Santa Monica Blvd", "West Hollywood, CA 90069"]}, "phone": "+13107770310", "display_phone": "(310) 777-0310", "distance": 6411.620748370255}, {"id": "5xFBQiB4Jcy9sI2fUHfcpg", "alias": "mariscos-jalisco-los-angeles-3", "name": "Mariscos Jalisco", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/82aNoyqi6hEhN4cxyhq1hA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/mariscos-jalisco-los-angeles-3?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 844, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "seafood", "title": "Seafood"}, {"alias": "foodtrucks", "title": "Food Trucks"}], "rating": 4.5, "coordinates": {"latitude": 34.021171, "longitude": -118.21276}, "transactions": [], "price": "$", "location": {"address1": "3040 E Olympic Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90023", "country": "US", "state": "CA", "display_address": ["3040 E Olympic Blvd", "Los Angeles, CA 90023"]}, "phone": "+13235286701", "display_phone": "(323) 528-6701", "distance": 10971.078871219486}, {"id": "eTDN9KE5LAjQdai_8J6pZg", "alias": "villa-moreliana-los-angeles-4", "name": "Villa Moreliana", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/O09vb43gfSIwW_VqRmMeWA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/villa-moreliana-los-angeles-4?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 397, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.5, "coordinates": {"latitude": 34.0505289, "longitude": -118.2486189}, "transactions": ["pickup"], "price": "$", "location": {"address1": "317 S Broadway", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90013", "country": "US", "state": "CA", "display_address": ["317 S Broadway", "Los Angeles, CA 90013"]}, "phone": "+12137250848", "display_phone": "(213) 725-0848", "distance": 6796.48850231388}, {"id": "KMZKgZCy6jJ3vbP_XL8rHA", "alias": "el-taurino-los-angeles-2", "name": "El Taurino", "image_url": "https://s3-media4.fl.yelpcdn.com/bphoto/FLU7E4LUzgEoog2n17pNlQ/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/el-taurino-los-angeles-2?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 2066, "categories": [{"alias": "tacos", "title": "Tacos"}], "rating": 3.5, "coordinates": {"latitude": 34.051414, "longitude": -118.283775}, "transactions": [], "price": "$", "location": {"address1": "1104 S Hoover St", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90006", "country": "US", "state": "CA", "display_address": ["1104 S Hoover St", "Los Angeles, CA 90006"]}, "phone": "+12137389197", "display_phone": "(213) 738-9197", "distance": 3631.9273738950005}, {"id": "5XZdMmOjLjvdZwgRzro6lQ", "alias": "leos-tacos-truck-los-angeles-3", "name": "Leo's Tacos Truck", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/71MC3HhqH1wpV5jgZVINMg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/leos-tacos-truck-los-angeles-3?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 330, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "foodtrucks", "title": "Food Trucks"}], "rating": 4.0, "coordinates": {"latitude": 34.067768, "longitude": -118.261123}, "transactions": [], "price": "$", "location": {"address1": "415 Glendale Blvd", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90026", "country": "US", "state": "CA", "display_address": ["415 Glendale Blvd", "Los Angeles, CA 90026"]}, "phone": "+13233462001", "display_phone": "(323) 346-2001", "distance": 5592.794662671381}, {"id": "qrYyDAuC6eKfiSqgxbD8SA", "alias": "taqueria-tapatio-no-2-los-angeles", "name": "Taqueria Tapatio No. 2", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/honRsr67e-9gOKwbcTbwXg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/taqueria-tapatio-no-2-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 156, "categories": [{"alias": "tacos", "title": "Tacos"}], "rating": 4.0, "coordinates": {"latitude": 34.0840689, "longitude": -118.3260969}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "724 Vine St", "address2": null, "address3": null, "city": "Los Angeles", "zip_code": "90038", "country": "US", "state": "CA", "display_address": ["724 Vine St", "Los Angeles, CA 90038"]}, "phone": "+13239635050", "display_phone": "(323) 963-5050", "distance": 2537.0529876408696}, {"id": "NSo-x-9ieVLtqakrkSGIHg", "alias": "chicas-tacos-los-angeles", "name": "Chicas Tacos", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/jIO3tIKjOjsqROq61iFc_g/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/chicas-tacos-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 572, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "organic_stores", "title": "Organic Stores"}, {"alias": "vegetarian", "title": "Vegetarian"}], "rating": 4.0, "coordinates": {"latitude": 34.0456597932358, "longitude": -118.256342201499}, "transactions": ["pickup", "delivery"], "price": "$$", "location": {"address1": "728 S Olive St", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90014", "country": "US", "state": "CA", "display_address": ["728 S Olive St", "Los Angeles, CA 90014"]}, "phone": "+12138960373", "display_phone": "(213) 896-0373", "distance": 6248.093716319033}, {"id": "yyFYXpciJvklnRupotn6qw", "alias": "yucas-los-angeles", "name": "Yuca's", "image_url": "https://s3-media3.fl.yelpcdn.com/bphoto/6vZ3-3F6jUrdGtdv4qI9IA/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/yucas-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 765, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "burgers", "title": "Burgers"}, {"alias": "hotdog", "title": "Hot Dogs"}], "rating": 4.0, "coordinates": {"latitude": 34.10912, "longitude": -118.28707}, "transactions": ["pickup", "delivery"], "price": "$", "location": {"address1": "2056 Hillhurst Ave", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90027", "country": "US", "state": "CA", "display_address": ["2056 Hillhurst Ave", "Los Angeles, CA 90027"]}, "phone": "+13236621214", "display_phone": "(323) 662-1214", "distance": 6148.052808113847}, {"id": "UNhsuZmtqGA5X5_3_pGYUw", "alias": "tacos-y-birria-la-unica-los-angeles", "name": "Tacos y Birria La Unica", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/Ih0E6hGhNoLW9DZhjQQylg/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/tacos-y-birria-la-unica-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 363, "categories": [{"alias": "tacos", "title": "Tacos"}, {"alias": "foodtrucks", "title": "Food Trucks"}], "rating": 4.5, "coordinates": {"latitude": 34.02252, "longitude": -118.21658}, "transactions": [], "price": "$", "location": {"address1": "2840 E Olympic Blvd", "address2": null, "address3": null, "city": "Los Angeles", "zip_code": "90023", "country": "US", "state": "CA", "display_address": ["2840 E Olympic Blvd", "Los Angeles, CA 90023"]}, "phone": "+13237154025", "display_phone": "(323) 715-4025", "distance": 10585.257452569478}, {"id": "taMhm9egDVqBzT3LEf7eHA", "alias": "pinches-tacos-los-angeles-5", "name": "Pinches Tacos", "image_url": "https://s3-media2.fl.yelpcdn.com/bphoto/jZODVxEAjUVacX7m2H_uRQ/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/pinches-tacos-los-angeles-5?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 228, "categories": [{"alias": "mexican", "title": "Mexican"}], "rating": 4.0, "coordinates": {"latitude": 34.0618827, "longitude": -118.4439995}, "transactions": [], "price": "$$", "location": {"address1": "1030 Glendon Ave", "address2": "", "address3": null, "city": "Los Angeles", "zip_code": "90024", "country": "US", "state": "CA", "display_address": ["1030 Glendon Ave", "Los Angeles, CA 90024"]}, "phone": "+13103618566", "display_phone": "(310) 361-8566", "distance": 11290.645458193736}, {"id": "Ij1S9QX1QWDVwJLBcWiejQ", "alias": "happy-taco-los-angeles", "name": "Happy Taco", "image_url": "https://s3-media1.fl.yelpcdn.com/bphoto/uxw_uKgSGeRhzm5z5zpguw/o.jpg", "is_closed": false, "url": "https://www.yelp.com/biz/happy-taco-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ", "review_count": 369, "categories": [{"alias": "mexican", "title": "Mexican"}, {"alias": "tradamerican", "title": "American (Traditional)"}], "rating": 4.0, "coordinates": {"latitude": 34.0470681, "longitude": -118.2843635}, "transactions": ["pickup", "delivery"], "price": "$$", "location": {"address1": "1309 S Hoover St", "address2": "", "address3": "", "city": "Los Angeles", "zip_code": "90006", "country": "US", "state": "CA", "display_address": ["1309 S Hoover St", "Los Angeles, CA 90006"]}, "phone": "+12133529313", "display_phone": "(213) 352-9313", "distance": 3773.1856658923807}], "total": 7800, "region": {"center": {"longitude": -118.32138061523438, "latitude": 34.0615895441259}}}


## Pagination

Now that you have an initial response, you can examine the contents of the JSON container. For example, you might start with ```response.json().keys()```. Here, you'll see a key for `'total'`, which tells you the full number of matching results given your query parameters. Write a loop (or ideally a function) which then makes successive API calls using the offset parameter to retrieve all of the results (or 5000 for a particularly large result set) for the original query. As you do this, be mindful of how you store the data. Your final goal will be to reformat the data concerning the businesses themselves into a pandas DataFrame from the json objects.

**Note: be mindful of the API rate limits. You can only make 5000 requests per day and are also can make requests too fast. Start prototyping small before running a loop that could be faulty. You can also use time.sleep(n) to add delays. For more details see https://www.yelp.com/developers/documentation/v3/rate_limiting.**


```python
# Your code here; use a function or loop to retrieve all the results from your original request
r.json().keys()
```




    dict_keys(['businesses', 'total', 'region'])




```python
for key in r.json().keys():
    print(key)
    value = r.json()[key] #Use standard dictionary formatting
    print(type(value)) #What type is it?
    print('\n\n') #Separate out data
```

    businesses
    <class 'list'>
    
    
    
    total
    <class 'int'>
    
    
    
    region
    <class 'dict'>
    
    
    



```python
r.json()['businesses'][:2]
```




    [{'id': 'QKovUc1TmSNtZh0j5ZEagw',
      'alias': 'leos-tacos-truck-los-angeles',
      'name': "Leo's Tacos Truck",
      'image_url': 'https://s3-media1.fl.yelpcdn.com/bphoto/EnENQLNGtYeRoN1BXM_tBw/o.jpg',
      'is_closed': False,
      'url': 'https://www.yelp.com/biz/leos-tacos-truck-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ',
      'review_count': 1770,
      'categories': [{'alias': 'foodtrucks', 'title': 'Food Trucks'},
       {'alias': 'tacos', 'title': 'Tacos'}],
      'rating': 4.5,
      'coordinates': {'latitude': 34.046438, 'longitude': -118.345718},
      'transactions': [],
      'price': '$',
      'location': {'address1': '1515 S La Brea Ave',
       'address2': '',
       'address3': '',
       'city': 'Los Angeles',
       'zip_code': '90019',
       'country': 'US',
       'state': 'CA',
       'display_address': ['1515 S La Brea Ave', 'Los Angeles, CA 90019']},
      'phone': '+13233462001',
      'display_phone': '(323) 346-2001',
      'distance': 2804.549874934235},
     {'id': 'UUlGlPfy0FsReQ2LWWBSWw',
      'alias': 'tire-shop-taqueria-los-angeles',
      'name': 'Tire Shop Taqueria',
      'image_url': 'https://s3-media4.fl.yelpcdn.com/bphoto/bYCXExdvf9S94Ssmk5xpIA/o.jpg',
      'is_closed': False,
      'url': 'https://www.yelp.com/biz/tire-shop-taqueria-los-angeles?adjust_creative=12IMkq-xxD-jcDHiyMb-IQ&utm_campaign=yelp_api_v3&utm_medium=api_v3_business_search&utm_source=12IMkq-xxD-jcDHiyMb-IQ',
      'review_count': 433,
      'categories': [{'alias': 'mexican', 'title': 'Mexican'}],
      'rating': 4.5,
      'coordinates': {'latitude': 34.0097381464074,
       'longitude': -118.265285901725},
      'transactions': [],
      'price': '$',
      'location': {'address1': '4069 S Avalon Blvd',
       'address2': '',
       'address3': '',
       'city': 'Los Angeles',
       'zip_code': '90011',
       'country': 'US',
       'state': 'CA',
       'display_address': ['4069 S Avalon Blvd', 'Los Angeles, CA 90011']},
      'phone': '+13232352812',
      'display_phone': '(323) 235-2812',
      'distance': 7743.367507855298}]




```python
r.json()['total']
```




    7800




```python
r.json()['region']
```




    {'center': {'longitude': -118.32138061523438, 'latitude': 34.0615895441259}}



## Exploratory Analysis

Take the restaurants from the previous question and do an initial exploratory analysis. At minimum, this should include looking at the distribution of features such as price, rating and number of reviews as well as the relations between these dimensions.


```python
import pandas as pd

df = pd.DataFrame.from_dict(r.json()['businesses'])
print(len(df)) #Print how many rows
print(df.columns) #Print column names
df.head() #Previews the first five rows. 
```

    50
    Index(['id', 'alias', 'name', 'image_url', 'is_closed', 'url', 'review_count',
           'categories', 'rating', 'coordinates', 'transactions', 'price',
           'location', 'phone', 'display_phone', 'distance'],
          dtype='object')





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
      <th>id</th>
      <th>alias</th>
      <th>name</th>
      <th>image_url</th>
      <th>is_closed</th>
      <th>url</th>
      <th>review_count</th>
      <th>categories</th>
      <th>rating</th>
      <th>coordinates</th>
      <th>transactions</th>
      <th>price</th>
      <th>location</th>
      <th>phone</th>
      <th>display_phone</th>
      <th>distance</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>QKovUc1TmSNtZh0j5ZEagw</td>
      <td>leos-tacos-truck-los-angeles</td>
      <td>Leo's Tacos Truck</td>
      <td>https://s3-media1.fl.yelpcdn.com/bphoto/EnENQL...</td>
      <td>False</td>
      <td>https://www.yelp.com/biz/leos-tacos-truck-los-...</td>
      <td>1770</td>
      <td>[{'alias': 'foodtrucks', 'title': 'Food Trucks...</td>
      <td>4.5</td>
      <td>{'latitude': 34.046438, 'longitude': -118.345718}</td>
      <td>[]</td>
      <td>$</td>
      <td>{'address1': '1515 S La Brea Ave', 'address2':...</td>
      <td>+13233462001</td>
      <td>(323) 346-2001</td>
      <td>2804.549875</td>
    </tr>
    <tr>
      <td>1</td>
      <td>UUlGlPfy0FsReQ2LWWBSWw</td>
      <td>tire-shop-taqueria-los-angeles</td>
      <td>Tire Shop Taqueria</td>
      <td>https://s3-media4.fl.yelpcdn.com/bphoto/bYCXEx...</td>
      <td>False</td>
      <td>https://www.yelp.com/biz/tire-shop-taqueria-lo...</td>
      <td>433</td>
      <td>[{'alias': 'mexican', 'title': 'Mexican'}]</td>
      <td>4.5</td>
      <td>{'latitude': 34.0097381464074, 'longitude': -1...</td>
      <td>[]</td>
      <td>$</td>
      <td>{'address1': '4069 S Avalon Blvd', 'address2':...</td>
      <td>+13232352812</td>
      <td>(323) 235-2812</td>
      <td>7743.367508</td>
    </tr>
    <tr>
      <td>2</td>
      <td>VqG_rccDoUXreiaHH0DEUA</td>
      <td>guisados-dtla-los-angeles-2</td>
      <td>Guisados DTLA</td>
      <td>https://s3-media2.fl.yelpcdn.com/bphoto/n_BRYn...</td>
      <td>False</td>
      <td>https://www.yelp.com/biz/guisados-dtla-los-ang...</td>
      <td>1724</td>
      <td>[{'alias': 'mexican', 'title': 'Mexican'}]</td>
      <td>4.5</td>
      <td>{'latitude': 34.0465305975394, 'longitude': -1...</td>
      <td>[pickup, delivery]</td>
      <td>$</td>
      <td>{'address1': '541 S Spring St', 'address2': 'S...</td>
      <td>+12136277656</td>
      <td>(213) 627-7656</td>
      <td>6725.171634</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Ti2Ksp2oPj6rpdp2tQcaVA</td>
      <td>sonoratown-los-angeles</td>
      <td>Sonoratown</td>
      <td>https://s3-media4.fl.yelpcdn.com/bphoto/wOFFBV...</td>
      <td>False</td>
      <td>https://www.yelp.com/biz/sonoratown-los-angele...</td>
      <td>814</td>
      <td>[{'alias': 'tacos', 'title': 'Tacos'}]</td>
      <td>4.5</td>
      <td>{'latitude': 34.0416441, 'longitude': -118.252...</td>
      <td>[]</td>
      <td>$</td>
      <td>{'address1': '208 E 8th St', 'address2': None,...</td>
      <td>+12136283710</td>
      <td>(213) 628-3710</td>
      <td>6738.523417</td>
    </tr>
    <tr>
      <td>4</td>
      <td>qpcH_Fub586U2kw_pQJS3w</td>
      <td>guisados-los-angeles</td>
      <td>Guisados</td>
      <td>https://s3-media4.fl.yelpcdn.com/bphoto/XgalCU...</td>
      <td>False</td>
      <td>https://www.yelp.com/biz/guisados-los-angeles?...</td>
      <td>2413</td>
      <td>[{'alias': 'mexican', 'title': 'Mexican'}, {'a...</td>
      <td>4.5</td>
      <td>{'latitude': 34.04812, 'longitude': -118.21147}</td>
      <td>[pickup, delivery]</td>
      <td>$</td>
      <td>{'address1': '2100 East Cesar E Chavez Ave', '...</td>
      <td>+13232647201</td>
      <td>(323) 264-7201</td>
      <td>10234.474965</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 50 entries, 0 to 49
    Data columns (total 16 columns):
    id               50 non-null object
    alias            50 non-null object
    name             50 non-null object
    image_url        50 non-null object
    is_closed        50 non-null bool
    url              50 non-null object
    review_count     50 non-null int64
    categories       50 non-null object
    rating           50 non-null float64
    coordinates      50 non-null object
    transactions     50 non-null object
    price            50 non-null object
    location         50 non-null object
    phone            50 non-null object
    display_phone    50 non-null object
    distance         50 non-null float64
    dtypes: bool(1), float64(2), int64(1), object(12)
    memory usage: 6.0+ KB



```python
df.describe()
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
      <th>review_count</th>
      <th>rating</th>
      <th>distance</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>count</td>
      <td>50.000000</td>
      <td>50.000000</td>
      <td>50.000000</td>
    </tr>
    <tr>
      <td>mean</td>
      <td>775.800000</td>
      <td>4.250000</td>
      <td>6436.340278</td>
    </tr>
    <tr>
      <td>std</td>
      <td>600.284456</td>
      <td>0.338815</td>
      <td>3160.335147</td>
    </tr>
    <tr>
      <td>min</td>
      <td>35.000000</td>
      <td>3.500000</td>
      <td>1509.657826</td>
    </tr>
    <tr>
      <td>25%</td>
      <td>300.000000</td>
      <td>4.000000</td>
      <td>3823.161463</td>
    </tr>
    <tr>
      <td>50%</td>
      <td>688.500000</td>
      <td>4.500000</td>
      <td>6329.857232</td>
    </tr>
    <tr>
      <td>75%</td>
      <td>987.250000</td>
      <td>4.500000</td>
      <td>8414.715696</td>
    </tr>
    <tr>
      <td>max</td>
      <td>2413.000000</td>
      <td>5.000000</td>
      <td>14746.067220</td>
    </tr>
  </tbody>
</table>
</div>



## Mapping

Look at the initial Yelp example and try and make a map using Folium of the restaurants you retrieved. Be sure to also add popups to the markers giving some basic information such as name, rating and price.


```python
#!pip install folium
```


```python
import folium

lat = 34.0615895441259
long = -118.32138061523438
#Create a map of the area
base_map = folium.Map([lat, long], zoom_start=13)
base_map


import numpy as np


#Generate some random locations to add to our map
x = [lat + np.random.uniform(-.1,.1) for i in range(20)]
y = [long + np.random.uniform(-.1,.1) for i in range(20)]
points = list(zip(x, y))
for p in points:
    lat = p[0]
    long = p[1]
    marker = folium.Marker(location=[lat, long])
    marker.add_to(base_map)
base_map


for p in points:
    lat = p[0]
    long = p[1]
    popup_text = "Latitude: {}, Longitude: {}".format(lat,long)
    popup = folium.Popup(popup_text, parse_html=True)
    marker = folium.Marker(location=[lat, long], popup=popup)
    marker.add_to(base_map)
base_map
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgCiAgICAgICAgPHNjcmlwdD4KICAgICAgICAgICAgTF9OT19UT1VDSCA9IGZhbHNlOwogICAgICAgICAgICBMX0RJU0FCTEVfM0QgPSBmYWxzZTsKICAgICAgICA8L3NjcmlwdD4KICAgIAogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vY2RuLmpzZGVsaXZyLm5ldC9ucG0vbGVhZmxldEAxLjUuMS9kaXN0L2xlYWZsZXQuanMiPjwvc2NyaXB0PgogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vY29kZS5qcXVlcnkuY29tL2pxdWVyeS0xLjEyLjQubWluLmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9qcy9ib290c3RyYXAubWluLmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5qcyI+PC9zY3JpcHQ+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vY2RuLmpzZGVsaXZyLm5ldC9ucG0vbGVhZmxldEAxLjUuMS9kaXN0L2xlYWZsZXQuY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vYm9vdHN0cmFwLzMuMi4wL2Nzcy9ib290c3RyYXAubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLXRoZW1lLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9mb250LWF3ZXNvbWUvNC42LjMvY3NzL2ZvbnQtYXdlc29tZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vY2RuanMuY2xvdWRmbGFyZS5jb20vYWpheC9saWJzL0xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLzIuMC4yL2xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL3Jhd2Nkbi5naXRoYWNrLmNvbS9weXRob24tdmlzdWFsaXphdGlvbi9mb2xpdW0vbWFzdGVyL2ZvbGl1bS90ZW1wbGF0ZXMvbGVhZmxldC5hd2Vzb21lLnJvdGF0ZS5jc3MiLz4KICAgIDxzdHlsZT5odG1sLCBib2R5IHt3aWR0aDogMTAwJTtoZWlnaHQ6IDEwMCU7bWFyZ2luOiAwO3BhZGRpbmc6IDA7fTwvc3R5bGU+CiAgICA8c3R5bGU+I21hcCB7cG9zaXRpb246YWJzb2x1dGU7dG9wOjA7Ym90dG9tOjA7cmlnaHQ6MDtsZWZ0OjA7fTwvc3R5bGU+CiAgICAKICAgICAgICAgICAgPG1ldGEgbmFtZT0idmlld3BvcnQiIGNvbnRlbnQ9IndpZHRoPWRldmljZS13aWR0aCwKICAgICAgICAgICAgICAgIGluaXRpYWwtc2NhbGU9MS4wLCBtYXhpbXVtLXNjYWxlPTEuMCwgdXNlci1zY2FsYWJsZT1ubyIgLz4KICAgICAgICAgICAgPHN0eWxlPgogICAgICAgICAgICAgICAgI21hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCB7CiAgICAgICAgICAgICAgICAgICAgcG9zaXRpb246IHJlbGF0aXZlOwogICAgICAgICAgICAgICAgICAgIHdpZHRoOiAxMDAuMCU7CiAgICAgICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICAgICAgbGVmdDogMC4wJTsKICAgICAgICAgICAgICAgICAgICB0b3A6IDAuMCU7CiAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgIDwvc3R5bGU+CiAgICAgICAgCjwvaGVhZD4KPGJvZHk+ICAgIAogICAgCiAgICAgICAgICAgIDxkaXYgY2xhc3M9ImZvbGl1bS1tYXAiIGlkPSJtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgiID48L2Rpdj4KICAgICAgICAKPC9ib2R5Pgo8c2NyaXB0PiAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4ID0gTC5tYXAoCiAgICAgICAgICAgICAgICAibWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4IiwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBjZW50ZXI6IFszNC4wNjE1ODk1NDQxMjU5LCAtMTE4LjMyMTM4MDYxNTIzNDM4XSwKICAgICAgICAgICAgICAgICAgICBjcnM6IEwuQ1JTLkVQU0czODU3LAogICAgICAgICAgICAgICAgICAgIHpvb206IDEzLAogICAgICAgICAgICAgICAgICAgIHpvb21Db250cm9sOiB0cnVlLAogICAgICAgICAgICAgICAgICAgIHByZWZlckNhbnZhczogZmFsc2UsCiAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICk7CgogICAgICAgICAgICAKCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfMDcwMTMzZDM4NGExNGY0Mzg5NTQyYzk2ZmIzYmVjNzEgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICJodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZyIsCiAgICAgICAgICAgICAgICB7ImF0dHJpYnV0aW9uIjogIkRhdGEgYnkgXHUwMDI2Y29weTsgXHUwMDNjYSBocmVmPVwiaHR0cDovL29wZW5zdHJlZXRtYXAub3JnXCJcdTAwM2VPcGVuU3RyZWV0TWFwXHUwMDNjL2FcdTAwM2UsIHVuZGVyIFx1MDAzY2EgaHJlZj1cImh0dHA6Ly93d3cub3BlbnN0cmVldG1hcC5vcmcvY29weXJpZ2h0XCJcdTAwM2VPRGJMXHUwMDNjL2FcdTAwM2UuIiwgImRldGVjdFJldGluYSI6IGZhbHNlLCAibWF4TmF0aXZlWm9vbSI6IDE4LCAibWF4Wm9vbSI6IDE4LCAibWluWm9vbSI6IDAsICJub1dyYXAiOiBmYWxzZSwgIm9wYWNpdHkiOiAxLCAic3ViZG9tYWlucyI6ICJhYmMiLCAidG1zIjogZmFsc2V9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyXzBkMGQ5ZTg4YWE0ODQ2NmU4YmRhYzE0ZDNjY2ExNzNkID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMDM4Njg0MjAxMDMxLCAtMTE4LjI3NzYyMjk2NjAwMDkyXSwKICAgICAgICAgICAgICAgIHt9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyXzdmMjc4OGQwY2RlZTQ0NmVhN2FmYTkxOTQzMGRmMzJjID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMDU1NTA3NTk0MDI5OTgsIC0xMTguMzk2OTgxMDg5Nzk4ODddLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfNGRiYzIyZDUwZTE0NDE2ZTg2ZmJkNWExOGE3NGI5NjEgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszMy45ODEwMzg2NDU4NjA3ODUsIC0xMTguMjkwNjQzMTM1NzM2NjRdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfMWJlZGY1NmQwNGUyNGRjZWEyOGVlOGU3MDM2NDlmNjkgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4xMTk1Njg3OTU2NTA1MTUsIC0xMTguMjMwMTg1ODQ4NDg4OTRdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfY2Y0MTEwMTE4MWVmNGEyYjhkOTViZDUzMmIyMTgzMTAgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4wMDU2MDAyOTU2NTYzMSwgLTExOC4zMDM2NDI4NTMxNjMzMl0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl9kNmIzZjllZTY3MGQ0ZjBiODgyMTdmYjNkMmE5MGRiMyA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjA0NTAwNDUxMDIyNjMsIC0xMTguMzk2NTEyNTQ5NTU4ODVdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfYWI3Y2QxODc5M2E2NGNmNTkxYjAyNGRlZTE0OTc3NzAgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4xNTAzMTkxOTE1NDQ3NCwgLTExOC4yOTQxMDM0ODg4NDI0MV0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl9jODgyMDZiOWZjMDk0ZjJkYWRmYjYwZDI3NThhYjM5MSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjE1MDY0NDk4NDk1ODgyNCwgLTExOC4yMzQ2NzA4MDQyMzYzM10sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl9hNWEyODY4NmJmNDg0OGQxYWY3M2U3YmNiN2FkYjhiOCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjAxNzY0ODk0NzkxODY0LCAtMTE4LjQxNTA4NzY0NTk2NzE5XSwKICAgICAgICAgICAgICAgIHt9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyXzJjN2EyZDdjMzJmNzQwMzU4MDUyZTViOGU2OWM3ODljID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMTE0MTM2Njc1NjE0NDcsIC0xMTguMzY3ODU4NzM2NTk2MDhdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfMmU0N2QwZWEwZjgxNGRmOWI0YTU0MmFkYjE2ZjllYjYgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4xNDczNjI4MjUwOTgxMiwgLTExOC4yNjEwOTk1Mzc3MzIwMl0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl9hMGU5ODA1YzE3MDE0MjAxYmY2MzBiMWMwNzA1YjY4MSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjAzNzAyMDg5OTE1NTM3LCAtMTE4LjI1OTQwODQyNTg3MzE5XSwKICAgICAgICAgICAgICAgIHt9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyXzlkNjdjZjgwNTk1MTQ1NjE4YmIzZDJhZTYxZDA1N2JlID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMDQ4NTI1MzY1MTQ2NDcsIC0xMTguMzk0Njk5MDcxNzkyNzZdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfMzU0OTI4Njc0MTE5NGU2YWJkYTFjMjVhNjBlNTk3NDkgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4wMTA4OTUxMzI1OTQxMTUsIC0xMTguMjMyNTAwMzUzNDQ5NzFdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfNDdiYzFkNDFjNTc1NGFhNTljMWI5MzYxY2Q3YjdjZWMgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszMy45NjM2ODgyOTkyNzUzNTQsIC0xMTguMjYwODUzNDYxNzg3MTldLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfMTZhODJhYjJmNzUxNGI2MGJiOTI0NGFkZDMwMjY1MzQgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4xMTM5NDM0NTc2NzAyMSwgLTExOC40MDY5NzgyODE1NDc3MV0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl82MTEyMWU4NTc0Yzc0N2UzODU0YjBlMmVlNDhjY2I4OSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjAzNjU4ODEzNjA4MTAzLCAtMTE4LjI3NjUzNzA0OTg0NDM1XSwKICAgICAgICAgICAgICAgIHt9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyXzQyMzNjY2E3ZjBlNDQzNTNhNWNlYTc0YzlhMjliM2ZhID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMDExMjUxMjU0NTA2NTYsIC0xMTguMjk2NTIxMjM0Mzg0NTRdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfNzlmZmZkODdhZjI5NGQyODk2MzhkM2U4NmQ2MmMwNjMgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4xMzU5OTg0MjEzODg0NjQsIC0xMTguMzg2NzY3OTgwOTY5MjhdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfYTZhMDQzYzgwN2IzNDA0YWFkMjA4NGQzNzYzYjg0ODMgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4wMDcwODQwMDg1MDQ5NSwgLTExOC4zMzgwMzEwNjU0Nzc2OF0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl82YjRlYTM1OTVlMmQ0OTVmOGMzZGY3MTczYzRmNjlhNCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjAzODY4NDIwMTAzMSwgLTExOC4yNzc2MjI5NjYwMDA5Ml0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfNWQ3NTZlYzRhMmYyNGNmNTg0N2NlNzQ2MTJkNmM2MmEgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2Y2NzNkMjNkNmVkNzRmYTBhNDYyMmVkY2UzMDJlOTlhID0gJChgPGRpdiBpZD0iaHRtbF9mNjczZDIzZDZlZDc0ZmEwYTQ2MjJlZGNlMzAyZTk5YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF0aXR1ZGU6IDM0LjAzODY4NDIwMTAzMSwgTG9uZ2l0dWRlOiAtMTE4LjI3NzYyMjk2NjAwMDkyPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzVkNzU2ZWM0YTJmMjRjZjU4NDdjZTc0NjEyZDZjNjJhLnNldENvbnRlbnQoaHRtbF9mNjczZDIzZDZlZDc0ZmEwYTQ2MjJlZGNlMzAyZTk5YSk7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl82YjRlYTM1OTVlMmQ0OTVmOGMzZGY3MTczYzRmNjlhNC5iaW5kUG9wdXAocG9wdXBfNWQ3NTZlYzRhMmYyNGNmNTg0N2NlNzQ2MTJkNmM2MmEpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfMDNjZDJhOTVjNTU3NGY4OThmNmE5NDFhOGNkMWFjMjYgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4wNTU1MDc1OTQwMjk5OCwgLTExOC4zOTY5ODEwODk3OTg4N10sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfZWY0ODZiOGQ0NTEyNDk5OWJjZWZmNDJkMjhiMDNlYWMgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2MwOTBkOWM4MTdkYjRjMDZhOTkyZTI0OWI5MzY1YjRjID0gJChgPGRpdiBpZD0iaHRtbF9jMDkwZDljODE3ZGI0YzA2YTk5MmUyNDliOTM2NWI0YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF0aXR1ZGU6IDM0LjA1NTUwNzU5NDAyOTk4LCBMb25naXR1ZGU6IC0xMTguMzk2OTgxMDg5Nzk4ODc8L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfZWY0ODZiOGQ0NTEyNDk5OWJjZWZmNDJkMjhiMDNlYWMuc2V0Q29udGVudChodG1sX2MwOTBkOWM4MTdkYjRjMDZhOTkyZTI0OWI5MzY1YjRjKTsKICAgICAgICAKCiAgICAgICAgbWFya2VyXzAzY2QyYTk1YzU1NzRmODk4ZjZhOTQxYThjZDFhYzI2LmJpbmRQb3B1cChwb3B1cF9lZjQ4NmI4ZDQ1MTI0OTk5YmNlZmY0MmQyOGIwM2VhYykKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl83ODRhNWJiNWJkZTg0NWNkYTYwN2MwZmI0MjUzNDViYyA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzMzLjk4MTAzODY0NTg2MDc4NSwgLTExOC4yOTA2NDMxMzU3MzY2NF0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfOTMxZTYwMDRlMzdkNGQwMGFhOWM5NTBiNTIzY2JhMzUgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2I0NzE2NDNlMjk5NjRjMmFiMjdhZTJiNmJiNjI1YmMyID0gJChgPGRpdiBpZD0iaHRtbF9iNDcxNjQzZTI5OTY0YzJhYjI3YWUyYjZiYjYyNWJjMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF0aXR1ZGU6IDMzLjk4MTAzODY0NTg2MDc4NSwgTG9uZ2l0dWRlOiAtMTE4LjI5MDY0MzEzNTczNjY0PC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzkzMWU2MDA0ZTM3ZDRkMDBhYTljOTUwYjUyM2NiYTM1LnNldENvbnRlbnQoaHRtbF9iNDcxNjQzZTI5OTY0YzJhYjI3YWUyYjZiYjYyNWJjMik7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl83ODRhNWJiNWJkZTg0NWNkYTYwN2MwZmI0MjUzNDViYy5iaW5kUG9wdXAocG9wdXBfOTMxZTYwMDRlMzdkNGQwMGFhOWM5NTBiNTIzY2JhMzUpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfYzdmZmZlMWMxMTNhNDZkZDliOTVjNGY2OTU4YmI3OWYgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4xMTk1Njg3OTU2NTA1MTUsIC0xMTguMjMwMTg1ODQ4NDg4OTRdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2Y1YzgxZWY1Yjk0OTQyNTI4YmU4YTE1OWIzODRhMjI0ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9lMmEzYzViMDFjYTU0NTkyYmFiZWFlMDg4OGRkNDhlMiA9ICQoYDxkaXYgaWQ9Imh0bWxfZTJhM2M1YjAxY2E1NDU5MmJhYmVhZTA4ODhkZDQ4ZTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxhdGl0dWRlOiAzNC4xMTk1Njg3OTU2NTA1MTUsIExvbmdpdHVkZTogLTExOC4yMzAxODU4NDg0ODg5NDwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF9mNWM4MWVmNWI5NDk0MjUyOGJlOGExNTliMzg0YTIyNC5zZXRDb250ZW50KGh0bWxfZTJhM2M1YjAxY2E1NDU5MmJhYmVhZTA4ODhkZDQ4ZTIpOwogICAgICAgIAoKICAgICAgICBtYXJrZXJfYzdmZmZlMWMxMTNhNDZkZDliOTVjNGY2OTU4YmI3OWYuYmluZFBvcHVwKHBvcHVwX2Y1YzgxZWY1Yjk0OTQyNTI4YmU4YTE1OWIzODRhMjI0KQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyX2IyNGY1OGVkMzg1NzQ3ZTU4MGEzZjg2Mjk3MDNhOTk4ID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMDA1NjAwMjk1NjU2MzEsIC0xMTguMzAzNjQyODUzMTYzMzJdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzMzMTMxNjU2NmZlYTQ4NjI4Y2Q0NjliN2I0NjZlZDY3ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF85MTg1N2MxN2QyNTE0OWZiOTkwMTEwMTQxYzFjYzZjMyA9ICQoYDxkaXYgaWQ9Imh0bWxfOTE4NTdjMTdkMjUxNDlmYjk5MDExMDE0MWMxY2M2YzMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxhdGl0dWRlOiAzNC4wMDU2MDAyOTU2NTYzMSwgTG9uZ2l0dWRlOiAtMTE4LjMwMzY0Mjg1MzE2MzMyPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzMzMTMxNjU2NmZlYTQ4NjI4Y2Q0NjliN2I0NjZlZDY3LnNldENvbnRlbnQoaHRtbF85MTg1N2MxN2QyNTE0OWZiOTkwMTEwMTQxYzFjYzZjMyk7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl9iMjRmNThlZDM4NTc0N2U1ODBhM2Y4NjI5NzAzYTk5OC5iaW5kUG9wdXAocG9wdXBfMzMxMzE2NTY2ZmVhNDg2MjhjZDQ2OWI3YjQ2NmVkNjcpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfOTRiZjVkNmE3MTgzNGM3ZGEzMmUxYTRlY2MxMTA2MmUgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4wNDUwMDQ1MTAyMjYzLCAtMTE4LjM5NjUxMjU0OTU1ODg1XSwKICAgICAgICAgICAgICAgIHt9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF9hNzRlNjA1N2I3ZGY0NzRiODYyN2VkZTdjZTQwNjJkYiA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfNmQzZTEwYzkzZjgxNDJhNWFhNjRlZjA1YzUxNTliZjkgPSAkKGA8ZGl2IGlkPSJodG1sXzZkM2UxMGM5M2Y4MTQyYTVhYTY0ZWYwNWM1MTU5YmY5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXRpdHVkZTogMzQuMDQ1MDA0NTEwMjI2MywgTG9uZ2l0dWRlOiAtMTE4LjM5NjUxMjU0OTU1ODg1PC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwX2E3NGU2MDU3YjdkZjQ3NGI4NjI3ZWRlN2NlNDA2MmRiLnNldENvbnRlbnQoaHRtbF82ZDNlMTBjOTNmODE0MmE1YWE2NGVmMDVjNTE1OWJmOSk7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl85NGJmNWQ2YTcxODM0YzdkYTMyZTFhNGVjYzExMDYyZS5iaW5kUG9wdXAocG9wdXBfYTc0ZTYwNTdiN2RmNDc0Yjg2MjdlZGU3Y2U0MDYyZGIpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfMDQ3ZGMzYjk3ZDNhNGNkMDhjOWYzZGI5OGY1NTZlMGQgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4xNTAzMTkxOTE1NDQ3NCwgLTExOC4yOTQxMDM0ODg4NDI0MV0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfMzY1NDVhNGMxYjE0NGU3YzlmN2I4YmJkNWNiMDcwYTkgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sXzgwOWQ1MmE1YmU0ODQ3NTNhN2M5NTk1YTc0NWUwN2JlID0gJChgPGRpdiBpZD0iaHRtbF84MDlkNTJhNWJlNDg0NzUzYTdjOTU5NWE3NDVlMDdiZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF0aXR1ZGU6IDM0LjE1MDMxOTE5MTU0NDc0LCBMb25naXR1ZGU6IC0xMTguMjk0MTAzNDg4ODQyNDE8L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfMzY1NDVhNGMxYjE0NGU3YzlmN2I4YmJkNWNiMDcwYTkuc2V0Q29udGVudChodG1sXzgwOWQ1MmE1YmU0ODQ3NTNhN2M5NTk1YTc0NWUwN2JlKTsKICAgICAgICAKCiAgICAgICAgbWFya2VyXzA0N2RjM2I5N2QzYTRjZDA4YzlmM2RiOThmNTU2ZTBkLmJpbmRQb3B1cChwb3B1cF8zNjU0NWE0YzFiMTQ0ZTdjOWY3YjhiYmQ1Y2IwNzBhOSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl9iNWMxYjk5MGRlNmY0Yjg0OTBlNmQxNDIzODU2MjZmNyA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjE1MDY0NDk4NDk1ODgyNCwgLTExOC4yMzQ2NzA4MDQyMzYzM10sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfYzcyMTBlYzlkMDZjNDNkM2FhZmMwMTI1MmUxZWQ5ODkgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sXzM5YmUxM2YzYzQ2NTRjN2E5Nzg0NjMyOWNiODc4ZWE2ID0gJChgPGRpdiBpZD0iaHRtbF8zOWJlMTNmM2M0NjU0YzdhOTc4NDYzMjljYjg3OGVhNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF0aXR1ZGU6IDM0LjE1MDY0NDk4NDk1ODgyNCwgTG9uZ2l0dWRlOiAtMTE4LjIzNDY3MDgwNDIzNjMzPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwX2M3MjEwZWM5ZDA2YzQzZDNhYWZjMDEyNTJlMWVkOTg5LnNldENvbnRlbnQoaHRtbF8zOWJlMTNmM2M0NjU0YzdhOTc4NDYzMjljYjg3OGVhNik7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl9iNWMxYjk5MGRlNmY0Yjg0OTBlNmQxNDIzODU2MjZmNy5iaW5kUG9wdXAocG9wdXBfYzcyMTBlYzlkMDZjNDNkM2FhZmMwMTI1MmUxZWQ5ODkpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfYjFjM2QxMGY3MjU5NDg0Njg4MDA2Y2ViM2QyYmQ3Y2MgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4wMTc2NDg5NDc5MTg2NCwgLTExOC40MTUwODc2NDU5NjcxOV0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfYTNjNTcwMmUxODA3NDdmNjk5ZGE0MmJjMzk3ZjA0ZTAgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sXzlhMTcyMDk2YmY0MTQ5NDNhYjc5ZWQ3M2NiNGEzMjVlID0gJChgPGRpdiBpZD0iaHRtbF85YTE3MjA5NmJmNDE0OTQzYWI3OWVkNzNjYjRhMzI1ZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF0aXR1ZGU6IDM0LjAxNzY0ODk0NzkxODY0LCBMb25naXR1ZGU6IC0xMTguNDE1MDg3NjQ1OTY3MTk8L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfYTNjNTcwMmUxODA3NDdmNjk5ZGE0MmJjMzk3ZjA0ZTAuc2V0Q29udGVudChodG1sXzlhMTcyMDk2YmY0MTQ5NDNhYjc5ZWQ3M2NiNGEzMjVlKTsKICAgICAgICAKCiAgICAgICAgbWFya2VyX2IxYzNkMTBmNzI1OTQ4NDY4ODAwNmNlYjNkMmJkN2NjLmJpbmRQb3B1cChwb3B1cF9hM2M1NzAyZTE4MDc0N2Y2OTlkYTQyYmMzOTdmMDRlMCkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl9kNDBlZmUxNTU3MDg0ZGQ3OTEyZTMyOWNjMTU1ODMzOSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjExNDEzNjY3NTYxNDQ3LCAtMTE4LjM2Nzg1ODczNjU5NjA4XSwKICAgICAgICAgICAgICAgIHt9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF85MTVlNTQzMGRkNDM0NzYxYTg4ZmQzZjFkZjE3OTk5NCA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfZDRmZmM0YTkxNWM1NGI4YjhiZjQxNjEzNzY2ZGQ3NmUgPSAkKGA8ZGl2IGlkPSJodG1sX2Q0ZmZjNGE5MTVjNTRiOGI4YmY0MTYxMzc2NmRkNzZlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXRpdHVkZTogMzQuMTE0MTM2Njc1NjE0NDcsIExvbmdpdHVkZTogLTExOC4zNjc4NTg3MzY1OTYwODwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF85MTVlNTQzMGRkNDM0NzYxYTg4ZmQzZjFkZjE3OTk5NC5zZXRDb250ZW50KGh0bWxfZDRmZmM0YTkxNWM1NGI4YjhiZjQxNjEzNzY2ZGQ3NmUpOwogICAgICAgIAoKICAgICAgICBtYXJrZXJfZDQwZWZlMTU1NzA4NGRkNzkxMmUzMjljYzE1NTgzMzkuYmluZFBvcHVwKHBvcHVwXzkxNWU1NDMwZGQ0MzQ3NjFhODhmZDNmMWRmMTc5OTk0KQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyXzEwY2RiZTEyOWJmNjQyZGU5YTczODk5NzZjMmJhNTY2ID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMTQ3MzYyODI1MDk4MTIsIC0xMTguMjYxMDk5NTM3NzMyMDJdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2YyOTM5ZjA1OGRhMjQzNGNhYzhhNGRkYmZhM2Y3MTUyID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF82MzBlNTQ3MzYzY2M0MGNhOGRlYmRmYTJkNTgyYzExMSA9ICQoYDxkaXYgaWQ9Imh0bWxfNjMwZTU0NzM2M2NjNDBjYThkZWJkZmEyZDU4MmMxMTEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxhdGl0dWRlOiAzNC4xNDczNjI4MjUwOTgxMiwgTG9uZ2l0dWRlOiAtMTE4LjI2MTA5OTUzNzczMjAyPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwX2YyOTM5ZjA1OGRhMjQzNGNhYzhhNGRkYmZhM2Y3MTUyLnNldENvbnRlbnQoaHRtbF82MzBlNTQ3MzYzY2M0MGNhOGRlYmRmYTJkNTgyYzExMSk7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl8xMGNkYmUxMjliZjY0MmRlOWE3Mzg5OTc2YzJiYTU2Ni5iaW5kUG9wdXAocG9wdXBfZjI5MzlmMDU4ZGEyNDM0Y2FjOGE0ZGRiZmEzZjcxNTIpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfZDE1YjJmNjQ1ZmI0NGZmMDlmODI5NWQzMTRjOTQ4YTQgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4wMzcwMjA4OTkxNTUzNywgLTExOC4yNTk0MDg0MjU4NzMxOV0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfYjQzNDhlNGY5NjFlNGUyYWE3YzgyMTQ0ZTMzYzNmZGMgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2E4NDJjZTcxOGYyZDQ4OWNhOTIwOWY5OTYzZjlmYWUwID0gJChgPGRpdiBpZD0iaHRtbF9hODQyY2U3MThmMmQ0ODljYTkyMDlmOTk2M2Y5ZmFlMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF0aXR1ZGU6IDM0LjAzNzAyMDg5OTE1NTM3LCBMb25naXR1ZGU6IC0xMTguMjU5NDA4NDI1ODczMTk8L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfYjQzNDhlNGY5NjFlNGUyYWE3YzgyMTQ0ZTMzYzNmZGMuc2V0Q29udGVudChodG1sX2E4NDJjZTcxOGYyZDQ4OWNhOTIwOWY5OTYzZjlmYWUwKTsKICAgICAgICAKCiAgICAgICAgbWFya2VyX2QxNWIyZjY0NWZiNDRmZjA5ZjgyOTVkMzE0Yzk0OGE0LmJpbmRQb3B1cChwb3B1cF9iNDM0OGU0Zjk2MWU0ZTJhYTdjODIxNDRlMzNjM2ZkYykKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl8yNzQwNWFhMDQ3Mzk0MTdmOGU2ZjMxMTQ5MzEwNGQ5NiA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjA0ODUyNTM2NTE0NjQ3LCAtMTE4LjM5NDY5OTA3MTc5Mjc2XSwKICAgICAgICAgICAgICAgIHt9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF9kYThlZjkzMmRjMWI0ZGE4OTNkNDg1NDljODhlOGU1YyA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfYjI3N2Y4NDQ1NTVlNDVjMGFlMWU2NWYwMWI5Y2QxM2IgPSAkKGA8ZGl2IGlkPSJodG1sX2IyNzdmODQ0NTU1ZTQ1YzBhZTFlNjVmMDFiOWNkMTNiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXRpdHVkZTogMzQuMDQ4NTI1MzY1MTQ2NDcsIExvbmdpdHVkZTogLTExOC4zOTQ2OTkwNzE3OTI3NjwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF9kYThlZjkzMmRjMWI0ZGE4OTNkNDg1NDljODhlOGU1Yy5zZXRDb250ZW50KGh0bWxfYjI3N2Y4NDQ1NTVlNDVjMGFlMWU2NWYwMWI5Y2QxM2IpOwogICAgICAgIAoKICAgICAgICBtYXJrZXJfMjc0MDVhYTA0NzM5NDE3ZjhlNmYzMTE0OTMxMDRkOTYuYmluZFBvcHVwKHBvcHVwX2RhOGVmOTMyZGMxYjRkYTg5M2Q0ODU0OWM4OGU4ZTVjKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyXzQzMTFlY2FmZTZkZDRkOGQ4NDM3M2VhMDUwODA4OGU1ID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMDEwODk1MTMyNTk0MTE1LCAtMTE4LjIzMjUwMDM1MzQ0OTcxXSwKICAgICAgICAgICAgICAgIHt9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF84NWJjYmFjY2FlYzQ0ODQzYTI0MzE1MzZkNjQ1MjI0YiA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfNjYwNDVkOWMwODU2NDRlZThmYjI4ZTAzYTk0MjE3OWMgPSAkKGA8ZGl2IGlkPSJodG1sXzY2MDQ1ZDljMDg1NjQ0ZWU4ZmIyOGUwM2E5NDIxNzljIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXRpdHVkZTogMzQuMDEwODk1MTMyNTk0MTE1LCBMb25naXR1ZGU6IC0xMTguMjMyNTAwMzUzNDQ5NzE8L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfODViY2JhY2NhZWM0NDg0M2EyNDMxNTM2ZDY0NTIyNGIuc2V0Q29udGVudChodG1sXzY2MDQ1ZDljMDg1NjQ0ZWU4ZmIyOGUwM2E5NDIxNzljKTsKICAgICAgICAKCiAgICAgICAgbWFya2VyXzQzMTFlY2FmZTZkZDRkOGQ4NDM3M2VhMDUwODA4OGU1LmJpbmRQb3B1cChwb3B1cF84NWJjYmFjY2FlYzQ0ODQzYTI0MzE1MzZkNjQ1MjI0YikKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl9mYjlmMDJlNDMxOGU0NzA4ODlkYzYzODNlYmU0MWE4NSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzMzLjk2MzY4ODI5OTI3NTM1NCwgLTExOC4yNjA4NTM0NjE3ODcxOV0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfNWNiNDcxOTVlOTRmNGVlN2I4YTM3M2I1NGY0OWFkNTYgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2VmY2Y0NmI0ZGUxNDQ0MWE4NTE0Y2JiNmViNjgyMGQzID0gJChgPGRpdiBpZD0iaHRtbF9lZmNmNDZiNGRlMTQ0NDFhODUxNGNiYjZlYjY4MjBkMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF0aXR1ZGU6IDMzLjk2MzY4ODI5OTI3NTM1NCwgTG9uZ2l0dWRlOiAtMTE4LjI2MDg1MzQ2MTc4NzE5PC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzVjYjQ3MTk1ZTk0ZjRlZTdiOGEzNzNiNTRmNDlhZDU2LnNldENvbnRlbnQoaHRtbF9lZmNmNDZiNGRlMTQ0NDFhODUxNGNiYjZlYjY4MjBkMyk7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl9mYjlmMDJlNDMxOGU0NzA4ODlkYzYzODNlYmU0MWE4NS5iaW5kUG9wdXAocG9wdXBfNWNiNDcxOTVlOTRmNGVlN2I4YTM3M2I1NGY0OWFkNTYpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfYzgxOWNlMDU5YTg1NDgyZjg1OTM5YTMxZGY0YmQ2YjggPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4xMTM5NDM0NTc2NzAyMSwgLTExOC40MDY5NzgyODE1NDc3MV0sCiAgICAgICAgICAgICAgICB7fQogICAgICAgICAgICApLmFkZFRvKG1hcF9lYmFjYzUxYjFkMDY0ZjUzOTg0MmNjMDI2ZmE1ZjJjOCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfMjFlMmU1YmY2Y2JiNDc2ZWJkMzNmZGQxMjA2ZjJlNDkgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2I1MDhiNTM5MjQwMjQxYmViMzk0Y2RiYmI3ZDcyNmQyID0gJChgPGRpdiBpZD0iaHRtbF9iNTA4YjUzOTI0MDI0MWJlYjM5NGNkYmJiN2Q3MjZkMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF0aXR1ZGU6IDM0LjExMzk0MzQ1NzY3MDIxLCBMb25naXR1ZGU6IC0xMTguNDA2OTc4MjgxNTQ3NzE8L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfMjFlMmU1YmY2Y2JiNDc2ZWJkMzNmZGQxMjA2ZjJlNDkuc2V0Q29udGVudChodG1sX2I1MDhiNTM5MjQwMjQxYmViMzk0Y2RiYmI3ZDcyNmQyKTsKICAgICAgICAKCiAgICAgICAgbWFya2VyX2M4MTljZTA1OWE4NTQ4MmY4NTkzOWEzMWRmNGJkNmI4LmJpbmRQb3B1cChwb3B1cF8yMWUyZTViZjZjYmI0NzZlYmQzM2ZkZDEyMDZmMmU0OSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl8yOGQ3OTIzYmZiMmI0ODMzOWQ4MmY3ZWU1M2IzZWQ2MCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjAzNjU4ODEzNjA4MTAzLCAtMTE4LjI3NjUzNzA0OTg0NDM1XSwKICAgICAgICAgICAgICAgIHt9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2ViYWNjNTFiMWQwNjRmNTM5ODQyY2MwMjZmYTVmMmM4KTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF9jOWNkZDZiZmExNTk0MTI2YmUzNzNkODk2ZTUwNjRlZSA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfYzA4YjllOGIwZTY5NGNlYjg3ZDA4YzU2N2I2Y2FjYjAgPSAkKGA8ZGl2IGlkPSJodG1sX2MwOGI5ZThiMGU2OTRjZWI4N2QwOGM1NjdiNmNhY2IwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXRpdHVkZTogMzQuMDM2NTg4MTM2MDgxMDMsIExvbmdpdHVkZTogLTExOC4yNzY1MzcwNDk4NDQzNTwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF9jOWNkZDZiZmExNTk0MTI2YmUzNzNkODk2ZTUwNjRlZS5zZXRDb250ZW50KGh0bWxfYzA4YjllOGIwZTY5NGNlYjg3ZDA4YzU2N2I2Y2FjYjApOwogICAgICAgIAoKICAgICAgICBtYXJrZXJfMjhkNzkyM2JmYjJiNDgzMzlkODJmN2VlNTNiM2VkNjAuYmluZFBvcHVwKHBvcHVwX2M5Y2RkNmJmYTE1OTQxMjZiZTM3M2Q4OTZlNTA2NGVlKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyXzcxYWY5YWNmYWE1YzRmNzg4OWNhNzk0ZGJjZjZkOTNhID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMDExMjUxMjU0NTA2NTYsIC0xMTguMjk2NTIxMjM0Mzg0NTRdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2NiMTA2NzgzNDA1ZTRmZWVhZWFhNGRmYzMwNzY1MzRkID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF8yMzhmZjBkNTIyODg0NDg5YTY2Y2Q1NDQ5MTUwZDk1YyA9ICQoYDxkaXYgaWQ9Imh0bWxfMjM4ZmYwZDUyMjg4NDQ4OWE2NmNkNTQ0OTE1MGQ5NWMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxhdGl0dWRlOiAzNC4wMTEyNTEyNTQ1MDY1NiwgTG9uZ2l0dWRlOiAtMTE4LjI5NjUyMTIzNDM4NDU0PC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwX2NiMTA2NzgzNDA1ZTRmZWVhZWFhNGRmYzMwNzY1MzRkLnNldENvbnRlbnQoaHRtbF8yMzhmZjBkNTIyODg0NDg5YTY2Y2Q1NDQ5MTUwZDk1Yyk7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl83MWFmOWFjZmFhNWM0Zjc4ODljYTc5NGRiY2Y2ZDkzYS5iaW5kUG9wdXAocG9wdXBfY2IxMDY3ODM0MDVlNGZlZWFlYWE0ZGZjMzA3NjUzNGQpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBtYXJrZXJfZDQ4MjQ0MjZiMDUzNGU3MDlmMTdkMzc5ZGM3ZjU5ZTMgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4xMzU5OTg0MjEzODg0NjQsIC0xMTguMzg2NzY3OTgwOTY5MjhdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzlmZDVmM2I4MDQwMDRmY2M5N2U1NzgxYTJiZWQzNDE2ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF84NGI1NTM2NGU0OTQ0M2M2YjhlOWQxOWQzMDg0NzhhYSA9ICQoYDxkaXYgaWQ9Imh0bWxfODRiNTUzNjRlNDk0NDNjNmI4ZTlkMTlkMzA4NDc4YWEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxhdGl0dWRlOiAzNC4xMzU5OTg0MjEzODg0NjQsIExvbmdpdHVkZTogLTExOC4zODY3Njc5ODA5NjkyODwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF85ZmQ1ZjNiODA0MDA0ZmNjOTdlNTc4MWEyYmVkMzQxNi5zZXRDb250ZW50KGh0bWxfODRiNTUzNjRlNDk0NDNjNmI4ZTlkMTlkMzA4NDc4YWEpOwogICAgICAgIAoKICAgICAgICBtYXJrZXJfZDQ4MjQ0MjZiMDUzNGU3MDlmMTdkMzc5ZGM3ZjU5ZTMuYmluZFBvcHVwKHBvcHVwXzlmZDVmM2I4MDQwMDRmY2M5N2U1NzgxYTJiZWQzNDE2KQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyX2QwM2E4YWUyNWE1MjQyODViZmI1MjE0OTQ5MWE5YjllID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMDA3MDg0MDA4NTA0OTUsIC0xMTguMzM4MDMxMDY1NDc3NjhdLAogICAgICAgICAgICAgICAge30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWJhY2M1MWIxZDA2NGY1Mzk4NDJjYzAyNmZhNWYyYzgpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzU0MmMyZGM5ODExZjQzOTg5Zjc0ZWI5OWFjM2QzZjczID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF82YmY1ZGJjOTFiMzc0NzVjYjhmNzllNGE4OWZiNjdmOSA9ICQoYDxkaXYgaWQ9Imh0bWxfNmJmNWRiYzkxYjM3NDc1Y2I4Zjc5ZTRhODlmYjY3ZjkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxhdGl0dWRlOiAzNC4wMDcwODQwMDg1MDQ5NSwgTG9uZ2l0dWRlOiAtMTE4LjMzODAzMTA2NTQ3NzY4PC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzU0MmMyZGM5ODExZjQzOTg5Zjc0ZWI5OWFjM2QzZjczLnNldENvbnRlbnQoaHRtbF82YmY1ZGJjOTFiMzc0NzVjYjhmNzllNGE4OWZiNjdmOSk7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl9kMDNhOGFlMjVhNTI0Mjg1YmZiNTIxNDk0OTFhOWI5ZS5iaW5kUG9wdXAocG9wdXBfNTQyYzJkYzk4MTFmNDM5ODlmNzRlYjk5YWMzZDNmNzMpCiAgICAgICAgOwoKICAgICAgICAKICAgIAo8L3NjcmlwdD4=" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



## Summary

Nice work! In this lab, you've made multiple API calls to Yelp in order to paginate through a results set, performing some basic exploratory analysis and then creating a nice interactive map to display the results using Folium! Well done!
