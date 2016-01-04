##Angular Data Grid
Light and flexible Data Grid for AngularJS apps, with built-in sorting, pagination and filtering options, unified API for client-side and server-side data fetching, 
seamless synchronization with browser address bar and total freedom in mark-up and styling suitable for your application.

Demo Material Design: http://angular-data-grid.github.io/demo/material.html

Demo Bootstrap: http://angular-data-grid.github.io/

### Features
 - Does not have any hard-coded template so you can choose any mark-up you need, from basic ```<table>``` layout to any ```<div>``` structure.
 - Easily switch between the most popular Bootstrap and Google Material theming, or apply your own CSS theme just by changing several CSS classes.
 - Built-in sync with browser address bar (URL), so you can copy-n-paste sorting/filtering/pagination results URL and open it in other browser / send to anyone - even if pagination / filtering are done on a client-side. 
 - Unlike most part of other Angular DataGrids, we intentionally use non-isolated scope of the directive to maximize flexibility, so it can be easily synchronized with any data changes inside your controller. !With great power comes great responsibility, so be careful to use non-isolated API correctly.

### Installation

Using Bower:

```
bower install angular-data-grid
```

Using direct download: get ZIP archive [from here](https://github.com/angular-data-grid/angular-data-grid.github.io/archive/master.zip)
 
Then use files from ```dist``` folder (see below).

### Setup
1. Include scripts in you application: ```dataGrid.min.js``` and ```pagination.min.js``` (include the second one only if you need pagination), like: 
 
 ```javascript
 <script src="components/angular-data-grid/pagination.min.js"></script>
 <script src="components/angular-data-grid/dataGrid.min.js"></script>
 ```
 
2. Inject dataGrid dependency in your module:

 ```javascript
angular.module('myApp', ['dataGrid', 'pagination'])
 ```
 
3. Initialize grid with additional options in your controller. To do that, add ```grid-data``` directive to element and pass 2 required parameters ```grid-options``` and ```grid-actions```:

```HTML

 <div grid-data id='test' grid-options="gridOptions" grid-actions="gridActions">
 /// grid mark-up goes here
 </div>
 
 ```

 ```javascript
 $scope.gridOptions = {
                data: [], //required parameter - array with data
                //optional parameter - start sort options
                sort: {
                    predicate: 'companyName',
                    direction: 'asc'
                },
                //optional parameter - custom rules for filters (see explanation below)
                customFilters: {
                    startFrom: function (items, value, predicate) {
                        return items.filter(function (item) {
                            return value && item[predicate] ? !item[predicate].toLowerCase().indexOf(value.toLowerCase()) : true;
                        });
                    }
                }
                };
```

### Basic API

1. ```grid-options```: object in your controller with start options for grid. You must create this object with at least 1 required parameter - data.
2. ```grid-actions```:  object in your controller with functions for updating grid. You can  pass string or create empty object in controller. 
Use this object for calling methods of directive: ```sort()```, ```filter()```, ```refresh()```.
3. Inside ```grid-data``` directive you can use ```pagination``` directive.
4. Also you can use ```grid-item-per-page``` directive and pass into it array of values (e.g. 10, 25, 50). 
5. If you need get size of current displayed items you can use ```{{filtered.length}}``` value.
 
### Fetch Data
 - For client-side pagination/filtering to fetch all data at once: just assign ```gridOptions.data``` to any JSON array object.
 
 ```javascript 
 
 $scope.gridOptions = {
                 data: [], //required parameter - array with data
                 };
 
 ```
 
 - For server side pagination/filtering to fetch data by page: assign ```getData``` method to some function with URL params as 1st parameter and data itself as 2d parameter:
 
 ```javascript
    $scope.gridOptions = {
             getData: getServerData,
         };
    function getServerData(params, callback) {
           $http.get(contextPath + '/some/list' + params).then(function (response) {
           var data = response.data.some,
           totalItems = response.data.someCount;
           callback(data, totalItems);
           };       
```
       
### Sorting
To enable sorting, just add attribute ```sortable``` to your table headers. This will specify the property name you want to sort by. Also you can add class ```sortable``` to display acs/decs arrows.

```HTML
<th sortable="code" class="sortable">
    Order #
</th>
<th sortable="placed" class="sortable">
    Date Placed
</th>
```

```javascript
$scope.gridOptions = {
                data: [], //required parameter - array with data
                //optional parameter - start sort options
                sort: {
                    predicate: 'companyName',
                    direction: 'asc'
                }
                };
```

### Pagination

You can optionally use ```pagination``` directive to display paging with previous/next and first/last controls. 
Directive is built on a base of excellent [Angular UI](https://angular-ui.github.io/bootstrap/) component and share extensive API: 

```HTML
<pagination max-size="5" boundary-links="true" ng-if="paginationOptions.totalItems  > paginationOptions.itemsPerPage" total-items="paginationOptions.totalItems"
ng-model="paginationOptions.currentPage" ng-change="reloadGrid()" items-per-page="paginationOptions.itemsPerPage">
</pagination>
```

**Pagination Setting**
Settings can be provided as attributes in the <pagination> or globally configured through the paginationConfig.

 ```ng-change``` : ng-change can be used together with ng-model to call a function whenever the page changes.

 ```ng-model```  : Current page number. First page is 1.

 ```ng-disabled```  : Used to disable the pagination component

 ```total-items```  : Total number of items in all pages.

 ```items-per-page```  (Defaults: 10) : Maximum number of items per page. A value less than one indicates all items on one page.

 ```max-size```  (Defaults: null) : Limit number for pagination size.

 ```num-pages``` readonly (Defaults: angular.noop) : An optional expression assigned the total number of pages to display.

 ```rotate``` (Defaults: true) : Whether to keep current page in the middle of the visible ones.

 ```direction-links``` (Default: true) : Whether to display Previous / Next buttons.

 ```previous-text``` (Default: 'Previous') : Text for Previous button.

 ```next-text``` (Default: 'Next') : Text for Next button.

 ```boundary-links``` (Default: false) : Whether to display First / Last buttons.

 ```first-text``` (Default: 'First') : Text for First button.

 ```last-text``` (Default: 'Last') : Text for Last button.

 ```template-url``` (Default: 'template/pagination/pagination.html') : Override the template for the component with a custom provided template

### Filters
Data Grid supports 4 built-in types of filters: ```text```, ```select```, ```dateFrom``` and ```dateTo```. To use it, add attribute ```filter-by``` to any element and pass property name, which you want filtering. Also you need add attribute ```filter-type``` with type of filter. After that you need call ```filter()``` method in ```ng-change``` for text or select inputs and in ```ng-blur/ng-focus``` for datepickers. Filters synchronize with URL by ```ng-model``` value.

```HTML

 <input type="text" class="form-control order-search-box"
                                placeholder="Search By Order #"
                                ng-change="gridActions.filter()"
                                ng-model="code"
                                filter-by="code"
                                filter-type="text">

```

### Custom Filters
If you need use some custom filters (f.e. filter by first letter), you must add filter-by to specify property name, which you want filtering and add ng-model property. Then create in gridOptions.customFilters variable named as it ng-model value and contain filtering function. Filtering function accept items, value, predicate arguments and must return filtered array.

```javascript

 $scope.gridOptions = {
                data: [],
                customFilters: {
                    startFrom: function (items, value, predicate) {
                        return items.filter(function (item) {
                            return value && item[predicate] ? !item[predicate].toLowerCase().indexOf(value.toLowerCase()) : true;
                        });
                    }
                }
                };

```

### Others
All filters have parameter disable-url. If you set it as ```true```, URL-synchronization for this filter will be disabled. 
If you need use 2 or more grids on page, you must add id to grids, and use ```grid-id``` attribute on filters to specify their grid.