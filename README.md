# Commerce search dashboard chart

## Using this repo
To use this repo add it to your helm repos with
```shell
helm repo add mai-repo https://cybaspace.github.io/charts
```

## Preparations
### SAP Commerce preparation
To use the dashboard the searched terms must be transfered from SAP Commerce to the search dashboard service.  
Out of the box the search events only transfer _user_ and _base site id_. To enable the transfer of search terms the following impex must be executed in SAP Commerce.  
In this impex the _Default_Template_ will be configured. This will affect only destination targets (connections to kyma) that will be created in future.  
If an already created kyma connection should be configured the destination target should point to this target.
```impex
$destination_target=Default_Template
INSERT_UPDATE EventPropertyConfiguration;eventConfiguration(eventClass,destinationTarget(id[default=$destination_target]),version[default=1])[unique=true];propertyName[unique=true];propertyMapping;title;description;examples(key, value)[map-delimiter = |];required[default=true];type[default='string'];
"#% beforeEach:
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import de.hybris.platform.apiregistryservices.exceptions.EventPropertyConfigurationException;
import de.hybris.platform.apiregistryservices.utils.EventPropertyConfigurationValidationUtils;
String eventClass = line.get(Integer.valueOf(1));
String eventPC = line.get(Integer.valueOf(3));
try {
    Class.forName(eventClass);
} catch (ClassNotFoundException e) {
    line.clear();
}

try {
    EventPropertyConfigurationValidationUtils.validPropertyMapping(eventClass, eventPC);
} catch (EventPropertyConfigurationException e) {
    Logger LOG = LoggerFactory.getLogger(""ImpExImportReader"");
    LOG.info(""Event property configuration of event was skipped. {}"", e.getMessage());
    line.clear();
}
"
;de.hybris.eventtracking.model.events.SearchEvent          ; searchterm ; "event.searchTerm" ; "Search term" ; Search term user entered ; searchterm->sunglasses
;de.hybris.eventtracking.model.events.SearchNoResultsEvent ; searchterm ; "event.searchTerm" ; "Search term" ; Search term user entered ; searchterm->totallynonsense
```

### Kyma preparation
If you want a one-stop-installation resulting in a ready-to-use dashboard you must first connect SAP Commerce with kyma and then provide the application name in the installation (s. optional installation parameters).

## Installation
Install the search dashboard chart
```
helm install <release> mai-repo/search-dashboard <options>
```

### Mandatory installation parameters
- `namespace`  
  The namespace in which the dashboard should be installed must be provided.
  ```shell
  helm install <release> mai-repo/search-dashboard --namespace commercesearch
  ```
- `cluster.domain`  
  The public reachable cluster domain must be provided.  
  ```shell
  helm install <release> mai-repo/search-dashboard --set cluster.domain=xxxx.kyma.shoot.live.k8s-hana.ondemand.com
  ```

### Optional installation parameters
- `search-dashboard-api-function.commerce.app.name`  
  If kyma and the SAP commerce already connected apply the name of application under which SAP Commerce is registered in kyma to automatically create triggers to the function.  
  ```shell
  helm install <release> mai-repo/search-dashboard --set search-dashboard-api-function.commerce.app.name=sapcommerce
  ```

## After Installation
### Connection SAP Commerce with kyma
If the commerce app name is not supplied during installation the triggers won't be created.

Connect SAP commerce with the kyma installation.

Creating the triggers can easily obtained by upgrading the charts with the name of the SAP Commerce application.  
```shell
helm upgrade <release> mai-repo/search-dashboard --set search-dashboard-api-function.commerce.app.name=<app-name>
```
Alternatively the triggers also can be created via the kyma UI:
- Choose the namespace in which search dashboard is installed 
- goto functions
- open the installed _search-dashboard-api-function_
- select the tab _Configuration_
- press button `+ Add Event Trigger`
- search for the `tracking.search` and `tracking.searchnoresults` 
- add them to the function

## Open search dashboard
In kyma goto the namespace, choose _API Rules_ and click on the `Host` link in the row of search-dashboard.