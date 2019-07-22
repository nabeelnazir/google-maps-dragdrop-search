# google-maps-dragdrop-search
This repository will help in implementing a requirement where you can perform search by drag drop of Google map.

**Requirement:**
There is a google map and list of apartments present on a page. The reuqirement is to search apartments from Google map by mouse drag and drop events.

Following code is written in AngularJS, we have used [ng-map](https://ngmap.github.io/) in our AngularJS project. You can write it in whatever tool and technology you are working on because the concept is same. 


```
<ng-map on-dragend="dragEnd(map)" zoom="13" disable-default-u-i="true" zoom-control="true" scaleControl="false" scrollwheel="false" disable-double-click-zoom="false" center="[{{latlng[0]}}, {{latlng[1]}}]" street-view-control="false">
    <marker ng-repeat="p in properties"
            position="[{{p.latitude}},{{p.longitude}}]"
            title="{{p.property_apt_counts}} apartments in {{p.area}}"
            on-click="showDetail(event, p)"
            icon="/map_icon.png"
    >
    </marker>
</ng-map>
```
`ng-map` is a simplest way to show google map. Above code has `on-dragend="dragEnd(map)" ` which will help us to search apartments whenever the Google map drag event ended.


```
 $scope.dragEnd = function(event, map) {
      if ($scope.map_search_allowed && map !== undefined){
          loadSpinner();
          let theBounds = map.getBounds();
          let bounds = [[ theBounds.getSouthWest().lat(), theBounds.getSouthWest().lng()],[theBounds.getNorthEast().lat(), theBounds.getNorthEast().lng() ]];
          apartmentFactory.all($scope.q).then(function(data) {
              $scope.q.bounds = bounds;
              $scope.apartments = data.apartments;
              $scope.featured_apartments = data.featured_apartments;
              $scope.apartment_count = data.apartment_count;
              $scope.amenties = data.amenties_counts;
              $scope.areas = data.area_counts;
              $scope.properties = data.properties;
              $scope.order = (data.sortOrder == null ? 'default' : data.sortOrder);
              if ($scope.properties) {
                  $scope.array = latLngArray();
                  $scope.latlng = getLatLngCenter($scope.array);
              }
              $scope.marker_clicked = '';
              $scope.property_clicked = '';
              if ($scope.q.base_monthly_price_gteq !== undefined) { $scope.priceSlider.value = $scope.q.base_monthly_price_gteq; }
              $scope.priceSlider.maxValue = data.max_price;
              $scope.priceSlider.options.ceil = data.max_price;
              if ($scope.q.property_id_eq) {
                  $scope.property_listings_view = true;
              }
              unLoadSpinner();
              if ($scope.apartments) {
                  if ($scope.apartments[0].city_readable == "Barcelona") {
                      return ngMeta.setTitle(`Student Apartments in ${$scope.apartments[0].city_readable}`);
                  }else{
                      return ngMeta.setTitle(`Student Housing in ${$scope.apartments[0].city_readable}`);
                  }
              }
          });
      }
  };
```

Here is the breakdown of `dragEnd` method.

`let theBounds = map.getBounds();` This will fetch the bounds of the map.

`let bounds = [[ theBounds.getSouthWest().lat(), theBounds.getSouthWest().lng()],[theBounds.getNorthEast().lat(), theBounds.getNorthEast().lng() ]]; ` This will fetch the actual bounds of the portion of the map which is visible to the user after the drag ended on the Google maps.

`apartmentFactory.all($scope.q).then(function(data)` here is an `AngularJS` concept. This line of code is calling our backend API `index` method with all  
