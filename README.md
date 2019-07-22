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
Above `ng-map` is a simplest way to show google map. Above code has `on-dragend="dragEnd(map)" ` which will help us to search apartments whenever the Google map drag event ended.

**Here is out `dragEnd` function:**

```
$scope.dragEnd = function(event, map) {
    let theBounds = map.getBounds();
    let bounds = [[ theBounds.getSouthWest().lat(), theBounds.getSouthWest().lng()],[theBounds.getNorthEast().lat(), theBounds.getNorthEast().lng() ]];
    apartmentFactory.all($scope.q).then(function(data) {
        $scope.q.bounds = bounds;
        $scope.apartments = data.apartments;
    });
};
```

**Lets break down the `dragEnd` method:**

1. `let theBounds = map.getBounds();` This will fetch the bounds of the map.

2. `let bounds = [[ theBounds.getSouthWest().lat(), theBounds.getSouthWest().lng()],[theBounds.getNorthEast().lat(), theBounds.getNorthEast().lng() ]]; ` This will fetch the actual bounds of the portion of the map which is visible to the user after the drag event ended on the Google map.

3. `apartmentFactory.all($scope.q).then(function(data)` here is an `AngularJS` concept. This line of code is calling our backend API method namely `index` with a parameter of `bounds`. On backend we are using **Ruby on Rails** and a `geocoder gem`. On backend we have `Apartment` database table which has `latitude` & `longitude` columns. We are using `in_bounds` method of `geocoder gem` which will fetch all apartments with the matching bounds like this `apartments  = Apartment.in_bounds([bounds.first, bounds.second]).ids` Simple! This is it.
