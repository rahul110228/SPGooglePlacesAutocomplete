SPGooglePlacesAutocomplete
===============

SPGooglePlacesAutocomplete is a simple objective-c wrapper around the [Google Places Autocomplete API](https://developers.google.com/places/documentation/autocomplete). The API can be used to provide autocomplete functionality for text-based geographic searches, by returning Places such as businesses, addresses, and points of interest as a user types. SPGooglePlacesAutocomplete also provides support for converting Place results into CLPlacemark objects for easy mapping with MKMapView.

Screenshots
----
![](http://i.imgur.com/heUHG4w.png)
![](http://i.imgur.com/l0uZGXX.png)


How To Use It
-------------

### Requirements
SPGooglePlacesAutocomplete requires a deployment target >= iOS 6.0 and ARC.

### Installation
1. Create a new group in your project
2. Drag all the files from <clone directory>/SPGooglePlacesAutocomplete into this group
3. Include SPGooglePlacesAutocomplete wherever you need it with `#import "SPGooglePlacesAutocomplete.h"`.


### Performing Queries

Instantiate a new SPGooglePlacesAutocompleteQuery and fill in the properties you'd like to specify.

``` objective-c
#import "SPGooglePlacesAutocomplete.h"

...

SPGooglePlacesAutocompleteQuery *query = [[SPGooglePlacesAutocompleteQuery alloc] initWithApiKey:@"YourGoogleAPIKey"];
query.input = @"185 berry str"; // search key word
query.location = CLLocationCoordinate2DMake(37.76999, -122.44696);  // user's current location
query.radius = 100.0;   // search addresses close to user
query.language = @"en"; // optional
query.types = SPPlaceTypeGeocode; // Only return geocoding (address) results.
```

Then, call -fetchPlaces to ping Google's API and fetch results. The resulting array will return objects of the SPGooglePlacesAutocompletePlace class.

``` objective-c
[query fetchPlaces:^(NSArray *places, NSError *error) {
    NSLog(@"Places returned %@", places);
}];
```

If you need to update the query (for instance, as the user types), simply update the appropriate properties and call -fetchPlaces again. Any outstanding requests will automatically be cancelled and a new request with the updated properties will be issued.

### Resolving Places to CLPlacemarks

The Google Places Autocomplete API will return the names of Places that match your query. It will not, however, return lat-long information about these results. SPGooglePlacesAutocomplete handles this by resolving Place results to placemarks. Simply call -resolveToPlacemark on a SPGooglePlacesAutocompletePlace:

```objective-c
[query fetchPlaces:^(NSArray *places, NSError *error) {
    SPGooglePlacesAutocompletePlace *place = [places firstObject];
    if (place) {
        [place resolveToPlacemark:^(CLPlacemark *placemark, NSString *addressString, NSError *error) {
            NSLog(@"Placemark: %@", placemark);
        }];
    }
}];
```

When searching for "geocode" (address) Places, the library utilizes CLGeocoder to geocode the address. When searching for "establishment" (business) Places, the library will automatically ping [The Google Places API](https://developers.google.com/places/documentation/#PlaceDetailsRequests) to fetch the details needed to geolocate the business.

### Sample Code

For an example of how to use SPGooglePlacesAutocomplete, please see the included example project. I put a functional Google API key in the project, please don't use it elsewhere!
