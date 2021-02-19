# Commerce search dashboard chart

## Set the cluster domain
The public reachable cluster domain must be provided.  
e.g.
```
helm install <release> mai-repo/search-dashboard --set cluster.domain=xxxx.kyma.shoot.live.k8s-hana.ondemand.com
```

## Add triggers to function
To add triggers to the api function a list of triggers must be declared in a yaml file which then can be used in the installation.
```
search-dashboard-api-function:
  triggers:
    - name: <name>
      eventtype: <eventtype>
      eventtypeversion: <version>
      app: <application-name>
```
`eventypeversion` is optional (default is `v1`)

If a file `values.yaml` with following content exists
```
search-dashboard-api-function:
  triggers:
    - name: search
      eventtype: tracking.search
      app: commerce-cloud
    - name: searchnoresults
      eventtype: tracking.searchnoresults
      app: commerce-cloud
```
the installation can be started with  
`helm install -f values.yaml search-dashboard mai-repo/search-dashboard`