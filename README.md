# Introduction
This repository includes the necessary Python client libraries to access Kiali remotely

## Installation

To install, run ``python setup.py install`` if you installed from source code, or ``pip install kiali-client`` if using pip.


## Create Client Connections
To start a Kiali Client, use KialiClient() method. It requires the `host`, `username` and `password` parameters

```python
>>> from kiali import KialiClient
>>> client = KialiClient(host='kiali-url.com')
```

Another parameters possible to use with Client
* host (default: `localhost`)
* scheme (default: `http`, options: `https` and `http`
* port (default: `443`)
* auth_type (default: `https-user-password`, options: `no-auth`; oauth will be added)
* username (default: `admin`)
* password (default: `admin`)
* verify   (default: `False`) - used for verify SSL certificates
* swagger_address (default: https://raw.githubusercontent.com/kiali/kiali/master/swagger.json') - address to swagger file

## Response Metadata

- json
- text
- status_code
- url
- elapsed (Request response time)

Usage Examples:
```python
>>> client.request(method_name='getConfig').json()
{'istioNamespace': 'istio-system', 'istioLabels': {'AppLabelName': 'app', 'VersionLabelName': 'version'}}

>>> client.request(method_name='namespaceList').text
'[{"name":"bookinfo"},{"name":"bookinfo2"},{"name":"default"},{"name":"istio-examples"},{"name":"istio-system"},{"name":"management-infra"}]'

>>> client.request(method_name='namespaceMetrics', path={'namespace': 'istio-system'}).status_code
200

>>> client.request(method_name='jaegerInfo').url
'https://kiali-istio-system.host.com:443/api/jaeger'

>>> client.request(method_name='serviceList', path={'namespace': 'istio-system'}).elapsed
datetime.timedelta(seconds=1, microseconds=859915)
```
## Creating a Request on Kiali Client

## Methods Available

## Root

- This method will return the status of kiali.

Usage Example:
```python
>>> client.request(method_name='Root').json()
{'status': {'Kiali console version': '0.12.0-SNAPSHOT.2937-local-261fb4806afc5c853b097e3cd24e750c691ea6da', 'Kiali core commit hash':'d1efd8cf6b6a2c56301aa2bb14519b3904e258b0', 'Kiali core version': 'v0.12.0-SNAPSHOT', 'Kiali state': 'running'}, 'externalServices':[{'name':'Prometheus', 'version': '2.3.1'}, {'name': 'Kubernetes', 'version': 'v1.11.0+d4cacc0'}, {'name': 'Jaeger',   'url': 'https://jaeger-query-istio-system.host.com'}, {'name': 'Grafana', 'url': 'http://grafana-istio-system.host.com'}], 'warningMessages': []}
```

## jaegerInfo

- This method will return Jaeger URL.

Usage Example:
```python
>>> client.request(method_name='jaegerInfo').json()
{'url': 'https://jaeger-query-istio-system.host.com'}
```

## grafanaInfo

- This method will return Grafana URL and other descriptors.

```python
>>> client.request(method_name='grafanaInfo').json()
{'url': 'http://grafana-istio-system.host.com', 'serviceDashboardPath': '/d/LJ_uJAvmk/istio-service-dashboard', 'workloadDashboardPath': '/d/UbsSZTDik/istio-workload-dashboard', 'varNamespace': 'var-namespace', 'varService': 'var-service', 'varWorkload': 'var-workload'}
```

## getStatus

- This method will return the versions of kiali, kubernetes and Prometheus also will display grafana and Jaeger URL's.

Usage Example:
```python
>>> client.request(method_name='getStatus').json()
{'status': {'Kiali console version': '0.12.0-SNAPSHOT.2937-local-261fb4806afc5c853b097e3cd24e750c691ea6da', 'Kiali core commit hash':'d1efd8cf6b6a2c56301aa2bb14519b3904e258b0', 'Kiali core version': 'v0.12.0-SNAPSHOT', 'Kiali state': 'running'}, 'externalServices': [{'name': 'Prometheus', 'version': '2.3.1'}, {'name': 'Kubernetes', 'version': 'v1.11.0+d4cacc0'}, {'name': 'Jaeger', 'url': 'https://jaeger-query-istio-system.host.com'}, {'name': 'Grafana', 'url': 'http://grafana-istio-system.host.com'}], 'warningMessages': []}
```

## getConfig

- This method will return kiali config.

Usage Example:
```python
>>> client.request(method_name='getConfig').json()
{'istioNamespace': 'istio-system', 'istioLabels': {'AppLabelName': 'app', 'VersionLabelName': 'version'}}
```

## GetToken

- This method will return token information and the expiry date.

Usage Example:
```python
>>> client.request(method_name='GetToken').json()
{'token': '<TokenInformation>', 'expired_at': '2018-12-14 13:44:29.113973696 +0000 UTC m=+107438.008998940'}
```

## namespaceList

- This method will return list of available namespaces.

Usage Example:
```python
>>> client.request(method_name='namespaceList').json()
[{'name': 'bookinfo'}, {'name': 'bookinfo2'}, {'name': 'default'}, {'name': 'istio-examples'}, {'name': 'istio-system'}, {'name': 'management-infra'}]
```

## namespaceMetrics

- This method will return metrics of specified namespace
- Required Parameter (`namespace`)

Usage Example:
```python
>>> client.request(method_name='namespaceMetrics', path={'namespace': 'istio-system'}).json()
{'metrics': {'request_count': {'matrix': [{'metric': {}, 'values': [[1544707080, '1.982'], [1544707095, '2.036']
```

## namespaceHealth

- This method will return a dictonary with namespace `Health`
- Required Parameter (`namespace`)

Usage Example:
```python
>>> client.request(method_name='namespaceHealth', path={'namespace': 'istio-system'}).json()
{'details': {'envoy': [{'inbound': {'healthy': 0, 'total': 0}, 'outbound': {'healthy': 0, 'total': 0}, 'service': 'details'}], 'workloadStatuses':[{'name': 'details-v1', 'replicas': 1, 'available': 1}], 'requests': {'errorRatio': -1, 'inboundErrorRatio': -1, 'outboundErrorRatio': -1}}
```

## namespaceValidations

- This method will return validations for the specified namespace
- Required Parameter (`namespace`)

Usage Example:

```python
>>> client.request(method_name='namespaceValidations', path={'namespace': 'istio-system'}).json()
{'istio-system': {'destinationrule': {'istio-policy': {'name': 'istio-policy', 'objectType': 'destinationrule', 'valid': True, 'checks': []}, 'istio-telemetry': {'name': 'istio-telemetry', 'objectType': 'destinationrule', 'valid': True, 'checks': []}, 'service-b-destination-rule': {'name': 'service-b-destination-rule', 'objectType': 'destinationrule', 'valid': False, 'checks': [{'message': "Host doesn't have a valid service", 'severity': 'error', 'path': 'spec/host'}]}}
```

## istioConfigList

- This method will return istio configurations list for the specified namespace
- Required Parameter (`namespace`)

Usage Example:

```python
>>> client.request(method_name='istioConfigList', path={'namespace': 'istio-system'}).json()
{'namespace': {'name': 'istio-system'}, 'gateways': [{'metadata': {'name': 'istio-autogenerated-k8s-ingress', 'namespace': 'istio-system', 'selfLink': '/apis/networking.istio.io/v1alpha3/namespaces/istio-system/gateways/istio-autogenerated-k8s-ingress', 'uid': '0118413e-f273-11e8-8649-90b11c54aea7', 'resourceVersion': '11922069', 'generation': 1, 'creationTimestamp': '2018-11-27T18:33:58Z'}, 'spec': {'servers': [{'hosts': ['*'],  'port': {'name': 'http', 'number': 80, 'protocol': 'HTTP2'}}], 'selector': {'istio': 'ingress'}}}], 'virtualServices':{'permissions': {'update': False, 'delete': False}, 'items': []}
```

## istioConfigDetails

- This method will return istio configurations for the specified object in the specified namespace.
- Required Parameter (`namespace`, `object_type`, `object`)

```python
>>> client.request(method_name='istioConfigDetails', path={'namespace': 'istio-system', 'object_type': 'rules', 'object': 'promtcp'}).json()
{'namespace': {'name': 'istio-system'}, 'objectType': 'rules', 'gateway': None, 'virtualService': None, 'destinationRule': None, 'serviceEntry': None,
 'rule': {'metadata': {'name': 'promtcp',   'namespace': 'istio-system', 'selfLink': '/apis/config.istio.io/v1alpha2/namespaces/istio-system/rules/promtcp', 'uid': '01a64cfb-f273-11e8-8649-90b11c54aea7', 'resourceVersion': '11922143', 'generation': 1, 'creationTimestamp': '2018-11-27T18:33:59Z'}
```

## objectValidations

- This method will return validations for the specified object in specified namespace
- Required Parameter (`namespace`, `object_type`, `object`)

```python
>>> client.request(method_name='objectValidations', path={'namespace': 'bookinfo', 'object_type': 'virtualservices', 'object': 'bookinfo-vs'}).json()
{'virtualservice': {'bookinfo-vs': {'name': 'bookinfo-vs', 'objectType': 'virtualservice', 'valid': True, 'checks': []}}}
```

## serviceList

- This method will return list of services in the specified namespace.
- Required Parameter (`namespace`)

```python
>>> client.request(method_name='serviceList', path={'namespace': 'istio-system'}).json()
{'namespace': {'name': 'istio-system'}, 'services': [{'name': 'grafana', 'istioSidecar': False, 'appLabel': True}, {'name': 'istio-citadel', 'istioSidecar': False, 'appLabel': False}, {'name': 'istio-egressgateway', 'istioSidecar': False, 'appLabel': True}, {'name': 'istio-galley', 'istioSidecar': False, 'appLabel': False]}
```

## serviceDetails

- This method will return details of a specified service in the specified namespace.
- Required Parameter (`namespace`, `service`)

```python
>>> client.request(method_name='serviceDetails', path={'namespace': 'istio-system', 'service': 'kiali'}).json()
{'service': {'name': 'kiali', 'createdAt': '2018-12-12T19:48:03Z', 'resourceVersion': '6513273', 'namespace': {'name': 'istio-system'}, 'labels': {'app': 'kiali', 'version': 'master'}
```

## serviceMetrics

- This method will return a dictonary of Service metrics.
- Required Parameter (`namespace`, `service`)

```python
>>> client.request(method_name='serviceMetrics', path={'namespace': 'istio-system', 'service': 'kiali'}).json()
{'source': {'metrics': {'request_count_in': {'matrix': []}, 'request_error_count_in': {'matrix': []}, 'tcp_received_in': {'matrix': []},   'tcp_sent_in': {'matrix': []}}
```

## serviceHealth

- This method will return a dictonary with service `Health`
- Required Parameter (`namespace`, `service`)

```python
>>> client.request(method_name='serviceHealth', path={'namespace': 'istio-system', 'service': 'kiali'}).json()
{'envoy': {'inbound': {'healthy': 0, 'total': 0}, 'outbound': {'healthy': 0, 'total': 0}}, 'requests': {'errorRatio': -1, 'inboundErrorRatio': -1,
  'outboundErrorRatio': -1}}
```

## serviceValidations

- This method will return validations for the specified service in specified namespace
- Required Parameter (`namespace`, `service`)

```python
>>> client.request(method_name='serviceValidations', path={'namespace': 'istio-system', 'service': 'kiali'}).json()
{'gateway': {'bookinfo-gateway': {'name': 'bookinfo-gateway', 'objectType': 'gateway', 'valid': False, 'checks': [{'message': 'More than one Gateway for same host port combination', 'severity': 'warning', 'path': 'spec/servers[0]/hosts[0]'}]}, 'istio-autogenerated-k8s-ingress': {'name': 'istio-autogenerated-k8s-ingress', 'objectType': 'gateway', 'valid': False, 'checks': [{'message': 'More than one Gateway for same host port combination',   'severity': 'warning', 'path': 'spec/servers[0]/hosts[0]'}]}}}
```

## appList

- This method will return list of applications in the specified namespace.
- Required Parameter (`namespace`)

```python
>>> client.request(method_name='appList', path={'namespace': 'istio-system'}).json()
{'namespace': {'name': 'istio-system'}, 'applications': [{'name': 'istio-grafana', 'istioSidecar': False}, {'name': 'pilot', 'istioSidecar': False},
  {'name': 'policy', 'istioSidecar': False}
```

## appMetrics

- This method will return a dictonary of application metrics.
- Required Parameter (`namespace`, `app`)

```python
>>> client.request(method_name='appMetrics', path={'namespace': 'istio-system', 'app': 'kiali'}).json()
{'source': {'metrics': {'request_count_in': {'matrix': []}, 'request_count_out': {'matrix': []}, 'request_error_count_in': {'matrix': []},   'request_error_count_out': {'matrix': []}
```

## appDetails

- This method will return details of specified application in the namespace.
- Required Parameter (`namespace`, `app`)

```python
>>> client.request(method_name='appDetails', path={'namespace': 'istio-system', 'app': 'kiali'}).json()
{'namespace': {'name': 'istio-system'}, 'name': 'kiali', 'workloads': [{'workloadName': 'kiali', 'istioSidecar': False}], 'serviceNames': ['kiali']}
```

## appHealth

- This method will return a dictonary with app `Health`
- Required Parameter (`namespace`, `app`)

```python
>>> client.request(method_name='appHealth', path={'namespace': 'istio-system', 'app': 'kiali'}).json()
{'envoy': [], 'workloadStatuses': [{'name': 'kiali', 'replicas': 1, 'available': 1}], 'requests': {'errorRatio': -1, 'inboundErrorRatio': -1,  'outboundErrorRatio': -1}}
```

## workloadList

- This method will return list of workloads in the specified namespace.
- Required Parameter (`namespace`)

```python
>>> client.request(method_name='workloadList', path={'namespace': 'istio-system'}).json()
{'namespace': {'name': 'istio-system'}, 'workloads': [{'name': 'grafana', 'type': 'Deployment', 'createdAt': '2018-12-13T22:46:40Z',   'resourceVersion': '6763633', 'istioSidecar': False, 'labels': {'app': 'grafana'}, 'appLabel': True, 'versionLabel': False, 'podCount': 1}
```

## workloadDetails

- This method will return details of specified workloads in the namespace.
- Required Parameter (`namespace`, `workload`)

```python
>>> client.request(method_name='workloadDetails', path={'namespace': 'bookinfo', 'workload':'details-v1'}).json()
{'name': 'details-v1', 'type': 'Deployment', 'createdAt': '2018-12-03T08:15:20Z', 'resourceVersion': '13160739', 'istioSidecar': True, 'labels': {'app': 'details', 'version': 'v1'}
```

## workloadHealth

- This method will return a dictonary with workload `Health`
- Required Parameter (`namespace`, `workload`)

```python
>>> client.request(method_name='workloadHealth', path={'namespace': 'bookinfo', 'workload':'details-v1'}).json()
{'workloadStatus': {'name': 'details-v1', 'replicas': 1, 'available': 1}, 'requests': {'errorRatio': -1, 'inboundErrorRatio': -1,  'outboundErrorRatio': -1}}
```

## workloadMetrics

- This method will return a dictonary of workload metrics.
- Required Parameter (`namespace`, `workload`)

```python
>>> client.request(method_name='workloadMetrics', path={'namespace': 'bookinfo', 'workload':'details-v1'}).json()
{'metrics': {'request_count': {'matrix': []}, 'request_error_count': {'matrix': []}, 'tcp_received': {'matrix': []}, 'tcp_sent': {'matrix': []}},
 'histograms': {'request_duration': {'avg': {'matrix': []}}, 'request_size': {'avg': {'matrix': []}}, 'response_size': {'avg': {'matrix': []}}}}
```

## graphNamespaces

- This method will return a `Graph`, containing a dictionary with array of `Node` and array of `Edges`
- Required Parameter (`namespaces`)
- Additional Parameters that can be included eg: {params={'graphType': 'app', duration: '60s'}}

```python
>>> client.request(method_name='graphNamespaces', params={'namespaces': 'bookinfo', 'graphType': 'versionedApp', 'duration': '60s'}).json()
>>> client.request(method_name='graphNamespaces', params={'namespaces': 'bookinfo', 'graphType': 'workload', 'duration': '60s'}).json()
>>> client.request(method_name='graphNamespaces', params={'namespaces': 'bookinfo','graphType': 'app', 'duration': '30m'}).json()
{'timestamp': 1544782038, 'graphType': 'app', 'elements': {'nodes': [{'data': {'id': '6cdb3cf3ee9a17772f13b295368e112a', 'nodeType': 'app',     'namespace': 'bookinfo', 'app': 'details', 'destServices': {'details': True}, 'hasCB': True}}
```

## graphService

- This method will return a `Graph`, containing a dictionary with array of `Node` and array of `Edges`
- Required Parameter (`namespace`)

```python
>>> client.request(method_name='graphService', path={'namespace': 'bookinfo', 'service': 'mongodb'}).json()
{'timestamp': 1544782155, 'graphType': 'workload', 'elements': {'nodes': [{'data': {'id': 'd377d11829f7fbb6a62c714f86d536d3', 'nodeType': 'workload', 'namespace': 'bookinfo', 'workload': 'mongodb-v1', 'app': 'mongodb', 'version': 'v1', 'destServices': {'mongodb': True}}}
```

## graphWorkload

- This method will return a `Graph`, containing a dictionary with array of `Node` and array of `Edges`
- Required Parameter (`namespace`)

```python
>>> client.request(method_name='graphWorkload', path={'namespace': 'bookinfo', 'workload': 'mongodb-v1'}).json()
{'timestamp': 1544783312, 'graphType': 'workload', 'elements': {'nodes': [{'data': {'id': 'd377d11829f7fbb6a62c714f86d536d3', 'nodeType': 'workload',  'namespace': 'bookinfo', 'workload': 'mongodb-v1', 'app': 'mongodb', 'version': 'v1', 'destServices': {'mongodb': True}}}
```
