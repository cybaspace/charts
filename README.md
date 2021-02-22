# Commerce search dashboard chart

## Preconditions
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