# ze-kubernetes-collector
## Installation

The commands below install Zebrium log collector as a Kubernetes DaemonSet. It runs one collector instance on each node in a Kubernetes cluster.

1. `kubectl create secret generic zlog-collector-config --from-literal=log-collector-url=https://YOUR_ZE_API_INSTANCE_NAME.zebrium.com --from-literal=auth-token=<YOUR_ZE_API_AUTH_TOKEN>`
2. `kubectl create -f https://raw.githubusercontent.com/zebrium/zlog-collector/master/templates/zlog-collector.yaml`

After a few minutes, logs should be viewable on Zebrium web UI.

## Configuration
You can add labels to your application so the Zebrium UI can display this information. By default, Zebrium's kubernetes log collector uses the following three labels to get software and test information from pods:
1. `zebrium.com/branch`: branch of application software
2. `zebrium.com/build`: build of application software
3. `zebrium.com/node`: kubernetes node id

## Environment variables
<table>
  <tr>
    <th>Description</th>
    <th>Environment Variable</th>
    <th>Default Value</th>
  </tr>
  <tr>
    <td>Kubernetes pod label name of software branch</td>
    <td>ZE_LABEL_BRANCH</td>
    <td>&quot;zebrium.com/branch&quot; label defined in your deployment.yaml file (see sample snippet below) </td>
  </tr>
  <tr>
    <td>Kubernetes pod label name for software build</td>
    <td>ZE_LABEL_BUILD</td>
    <td>&quot;zebrium.com/build&quot; label defined in your deployment.yaml file (see sample snippet below)</td>
  </tr>
  <tr>
    <td>Kubernetes node id</td>
    <td><b>None</b></td>
    <td>&quot;zebrium.com/node&quot; label defined in your deployment.yaml file (see sample snippet below)</td>
  </tr>
  <tr>
    <td>Path to Kubernetes container log directory </td>
    <td>CONTAINER_LOGS_PATH</td>
    <td>/var/log/containers/*.log</td>
  </tr>
  <tr>
    <td>Regular expression of name spaces to be excluded from log collecting</td>
    <td>EXCLUDE_NAMESPACE_REGEX</td>
    <td>&quot;&quot;</td>
  </tr>
  <tr>
    <td>Regular expression of pods to be excluded from log collecting</td>
    <td>EXCLUDE_POD_REGEX</td>
    <td>&quot;&quot;</td>
  </tr>
</table>

## Sample deployment.yaml snippet

```
   spec:
     selector:
       matchLabels:
         app: zapp
     replicas: 1
     template:
       metadata:
         labels:
           app: zapp
           app.kubernetes.io/name: {{ template "zapp.name" . }}
           app.kubernetes.io/instance: {{ .Release.Name | quote }}
           zebrium.com/build: {{ .Values.software.release }}            <-----
           zebrium.com/branch: {{ .Values.software.branch }}            <-----
           {{ if ne .Values.node "" }}
           zebrium.com/node: {{ .Values.node }}                         <-----
           {{ end }}
```

