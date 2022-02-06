Damit FileBeat auf die Kubernetes Metadata zugreifen kann muss die ressource "nodes" der cluster-Rolle hinzugefügt werden:

1. einmal alle clusterroles anzeigen lassen
'k get clusterroles -n efk'

2. details der clusterrole anzeigen lassen.  (-o yaml = im yaml Format)
'k get clusterroles efk-filebeat-cluster-role -n efk -o yaml'

3. die aktuelle speichern 
'k get clusterroles efk-filebeat-cluster-role -n efk -o yaml > efk-filebeat-cluster-role.yml'

4. in der aktuellen die Änderungen vornehmen
'vim efk-filebeat-cluster-role.yml'

5. mit der geänderten updaten
'k apply -f efk-filebeat-cluster-role.yml -n efk'

# Local one-node cluster

elasticsearch:
  roles:
    monitor: true
  extraEnvs:
    - name: ELASTIC_PASSWORD
      valueFrom:
        secretKeyRef:
          name: elastic-credentials
          key: password
    - name: ELASTIC_USERNAME
      valueFrom:
        secretKeyRef:
          name: elastic-credentials
          key: username
  resources:
    requests:
      cpu: "1000m"
      memory: "2Gi"
    limits:
      cpu: "1000m"
      memory: "2Gi"
  persistence:
    enabled: false
  nodeAffinity: 
    { }
  esConfig: 
    elasticsearch.yml: |
      xpack:
        security:
          enabled: true
          transport:
            ssl:
              enabled: true
        license:
          self_generated:
            type: basic
      discovery:
        type: single-node
kibana:
  resources:
    requests:
      cpu: "1000m"
      memory: "2Gi"
    limits:
      cpu: "1000m"
      memory: "2Gi"
  ingress:
    enabled: true
    hosts:
      - host: kibana.localhost
        paths:
          - path: /
    tls: []
  kibanaConfig: 
     kibana.yml: |
       elasticsearch:
        username: "kibana_system"
        password: "adminadmin"

Create secret: elastic-credentials
Enable scurity
Search envs

set passwords

./bin/elasticsearch-setup-passwords auto

secrets

Conf:
  Kibana (kibana_system)
  heartbeat (beats_system)
  filebeat deamon (beats_system)
  metricbeat and conf deamon (beats_system)