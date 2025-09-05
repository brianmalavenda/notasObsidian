
Conceptos: Documentos, Índices, Instancias de ES Nodos, Shards, Réplicas
Buscar más: 
* Como hacer una prueba de rendimiento para definir cuantos shards necesitamos en un índice?
[Guía de instalación - video](https://www.youtube.com/watch?v=dl8DSgq0y3Y&list=PLlNuLwK_vaGND4FMd7E55kM8JG7JpCgA0&index=3) 
[Guía de instalación - documento](https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-from-archive-on-linux-macos)

### **Ejecución de elastic en mi pc**

 Elasticsearch security features have been automatically configured!
✅ Authentication is enabled and cluster connections are encrypted.

ℹ️  Password for the elastic user (reset with `bin/elasticsearch-reset-password -u elastic`):
  MDePn+EwoBOV97+pyp-R

ℹ️  HTTP CA certificate SHA-256 fingerprint:
  1eaaa654f05e34dac71b35f7ce3696a9d7602f7659f05e179629e81b3839e865

ℹ️  Configure Kibana to use this cluster:
• Run Kibana and click the configuration link in the terminal when Kibana starts.
• Copy the following enrollment token and paste it into Kibana in your browser (valid for the next 30 minutes):
  eyJ2ZXIiOiI4LjE0LjAiLCJhZHIiOlsiMTcyLjI4LjIyOC4yMDE6OTIwMCJdLCJmZ3IiOiIxZWFhYTY1NGYwNWUzNGRhYzcxYjM1ZjdjZTM2OTZhOWQ3NjAyZjc2NTlmMDVlMTc5NjI5ZTgxYjM4MzllODY1Iiwia2V5Ijoidi1vM2lwZ0IyOGZicUZORGh5Qkg6S1BOOVN3U281YzJqUWMwdVN0X3pVdyJ9

ℹ️  Configure other nodes to join this cluster:
• On this node:
  ⁃ Create an enrollment token with `bin/elasticsearch-create-enrollment-token -s node`.
  ⁃ Uncomment the transport.host setting at the end of config/elasticsearch.yml.
  ⁃ Restart Elasticsearch.
• On other nodes:
  ⁃ Start Elasticsearch with `bin/elasticsearch --enrollment-token <token>`, using the enrollment token that you generated.

#### **Comprobar que esta corriendo**
//se movió de carpeta de instalación porque antes estaba dentro de un proyecto en la ruta ~/Repositorio/app-test/elasticsearch-9.1.1 y se movió a ~/elasticsearch-9.1.1

`curl --cacert config/certs/http_ca.crt -u elastic:MDePn+EwoBOV97+pyp-R https://localhost:9200`

```
{
  "name" : "brian",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "QEGcAc52TH2YIalnEuyuVw",
  "version" : {
    "number" : "9.1.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "5e94055934defa56e454868b7783b2a3b683785e",
    "build_date" : "2025-08-05T01:07:31.959947279Z",
    "build_snapshot" : false,
    "lucene_version" : "10.2.2",
    "minimum_wire_compatibility_version" : "8.19.0",
    "minimum_index_compatibility_version" : "8.0.0"
  },
  "tagline" : "You Know, for Search"
}
```


### Ejecutar localmente ES

``curl -fsSL https://elastic.co/start-local | sh``


______ _           _   _
 |  ____| |         | | (_)
 | |__  | | __ _ ___| |_ _  ___
 |  __| | |/ _` / __| __| |/ __|
 | |____| | (_| \__ \ |_| | (__
 |______|_|\__,_|___/\__|_|\___|
-------------------------------------------------
🚀 Run Elasticsearch and Kibana for local testing
-------------------------------------------------

ℹ️  Do not use this script in a production environment

⌛️ Setting up Elasticsearch and Kibana v9.1.1...

- Generated random passwords
- Created the elastic-start-local folder containing the files:
[+] Running 4/25ettings
 ⠹ elasticsearch [⣷⣿⣿⣿⣿⠀⠀⠀⠀⠀] Pulling                                                                             13.2s
[+] Running 25/25 Downloading [============================================>      ]  35.77MB/...                   5.9s
 ✔ elasticsearch Pulled                                                                                          131.7s
   ✔ 0ae987e86ed3 Pull complete                                                                                   10.1s
   ✔ e585750ee4af Pull complete                                                                                   10.8s
   ✔ 0d8eeb619489 Pull complete                                                                                   10.9s
   ✔ 9a0c59d5588f Pull complete                                                                                   10.9s
   ✔ 4ca545ee6d5d Pull complete                                                                                  149.8s
   ✔ f1691d83ddea Pull complete                                                                                  124.1s
   ✔ 28d9b15aece8 Pull complete                                                                                  124.2s
   ✔ 5ba695371321 Pull complete                                                                                  124.2s
   ✔ a6277fa97978 Pull complete                                                                                  124.2s
   ✔ 630f4b6daab5 Pull complete                                                                                  124.3s
 ✔ kibana_settings Pulled                                                                                        131.7s
 ✔ kibana Pulled                                                                                                 157.2s
   ✔ dbef0a27a5c6 Pull complete                                                                                   18.1s
   ✔ 6ecaa876fb37 Pull complete                                                                                  149.0s
   ✔ 898f5d2d8a0e Pull complete                                                                                  149.0s
   ✔ e48a7ead7695 Pull complete                                                                                  149.2s
   ✔ 0444c4e32674 Pull complete                                                                                  149.2s
   ✔ b2647658e297 Pull complete                                                                                  149.3s
   ✔ 3286e66e2ea4 Pull complete                                                                                  149.3s
   ✔ 13f91ec0b3fc Pull complete                                                                                  149.3s
   ✔ 00632bcc0434 Pull complete                                                                                  149.3s
   ✔ 0b8479640e0d Pull complete                                                                                  149.3s
   ✔ 2a71718072b9 Pull complete                                                                                  149.3s
   ✔ f28dbcf4cd4e Pull complete                                                                                  149.4s
[+] Running 5/60d Waiting                                                                                          4.5s
 ✔ Network elastic-start-local_default             Created                                                         0.1s
 ✔ Volume "elastic-start-local_dev-elasticsearch"  Created                                                         0.0s
 ✔ Volume "elastic-start-local_dev-kibana"         Created                                                         0.0s
 ⠋ Container es-local-dev                          Starting                                                        1.1s
 ✔ Container kibana-local-settings                 Created                                                         0.0s
 ✔ Container kibana-local-dev                      Created                                                         0.0s

##### **Acá falló porque el puerto estaba ocupado**

***Esto esta levantando un contenedor con ES y Kibana***

Error response from daemon: failed to set up container networking: driver failed programming external connectivity on endpoint es-local-dev (ff3e580db9d3f2960c3a60260d015f6c28122a3ee3edb918017c9e3b63d09754): failed to bind host port for 127.0.0.1:9200:172.20.0.2:9200/tcp: address already in use
Error: docker compose up --wait command failed!
An error log has been generated in error-start-local.log file.
If you need assistance, open an issue at https://github.com/elastic/start-local/issues

Acá se ve como cerrando la ejecución anterior y volviendo a correr el script para levantar local ambas aplicaciones se levantan 2 contenedores, hay 2 viejos que despues hay que eliminar de la instalación fallida del paso anterior.

 ______ _           _   _
 |  ____| |         | | (_)
 | |__  | | __ _ ___| |_ _  ___
 |  __| | |/ _` / __| __| |/ __|
 | |____| | (_| \__ \ |_| | (__
 |______|_|\__,_|___/\__|_|\___|
-------------------------------------------------
🚀 Run Elasticsearch and Kibana for local testing
-------------------------------------------------

ℹ️  Do not use this script in a production environment

⌛️ Setting up Elasticsearch and Kibana v9.1.1...

[+] Running 25/25m passwords
 ✔ kibana Pulled                                                                                              147.1s
   ✔ dbef0a27a5c6 Pull complete                                                                                18.1s
   ✔ 6ecaa876fb37 Pull complete                                                                               138.0s
   ✔ 898f5d2d8a0e Pull complete                                                                               138.9s
   ✔ e48a7ead7695 Pull complete                                                                               139.6s
   ✔ 0444c4e32674 Pull complete                                                                               139.6s
   ✔ b2647658e297 Pull complete                                                                               139.7s
   ✔ 3286e66e2ea4 Pull complete                                                                               139.7s
   ✔ 13f91ec0b3fc Pull complete                                                                               139.7s
   ✔ 00632bcc0434 Pull complete                                                                               139.7s
   ✔ 0b8479640e0d Pull complete                                                                               139.8s
   ✔ 2a71718072b9 Pull complete                                                                               139.8s
   ✔ f28dbcf4cd4e Pull complete                                                                               139.8s
 ✔ elasticsearch Pulled                                                                                       120.1s
   ✔ 0ae987e86ed3 Pull complete                                                                                 8.6s
   ✔ e585750ee4af Pull complete                                                                                 8.9s
   ✔ 0d8eeb619489 Pull complete                                                                                 9.0s
   ✔ 9a0c59d5588f Pull complete                                                                                 9.0s
   ✔ 4ca545ee6d5d Pull complete                                                                               140.6s
   ✔ f1691d83ddea Pull complete                                                                               112.8s
   ✔ 28d9b15aece8 Pull complete                                                                               113.2s
   ✔ 5ba695371321 Pull complete                                                                               113.3s
   ✔ a6277fa97978 Pull complete                                                                               113.3s
   ✔ 630f4b6daab5 Pull complete                                                                               113.3s
 ✔ kibana_settings Pulled                                                                                     120.1s
[+] Running 6/6
 ✔ Network elastic-start-local_default             Created                                                      0.0s
 ✔ Volume "elastic-start-local_dev-elasticsearch"  Created                                                      0.0s
 ✔ Volume "elastic-start-local_dev-kibana"         Created                                                      0.0s
 ✔ Container es-local-dev                          Healthy                                                     43.2s
 ✔ Container kibana-local-settings                 Exited                                                      42.7s
 ✔ Container kibana-local-dev                      Healthy                                                     82.7s

🎉 Congrats, Elasticsearch and Kibana are installed and running in Docker!

🌐 Open your browser at http://localhost:5601

   Username: elastic
   Password: pJGulEBw

🔌 Elasticsearch API endpoint: http://localhost:9200
🔑 API key: a0l1WWlwZ0I5bk5rNjlramdSQ3U6UFZ2cWNQYUJVV0NMQkNwbGM3N0ZOUQ==


Learn more at https://github.com/elastic/start-local

![[elastich_search_docker_01.png]]


#### Request exitoso

curl --location 'http://localhost:9200/personas/_doc/1' \
--header '==Authorization: ApiKey a0l1WWlwZ0I5bk5rNjlramdSQ3U6UFZ2cWNQYUJVV0NMQkNwbGM3N0ZOUQ'== \
--header 'Content-Type: application/json' \
--header 'Cookie: access_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJ0ZXN0MDhAdGVzdC5jb20iLCJpYXQiOjE3NDc3MzQ4MDgsImV4cCI6MTc0NzgyMTIwOH0.mXhVdIve5GmlzK5E8DlusCrZ2ge7qbyEzBSRK4C0zhQ' \
--data '{
    "nombre":"Olga",
    "apellido": "Duarte"
}'

lo destacado arriba es que es necesario usar la Authorization en el Header y que este tiene que ser con el prefijo ApiKey y luego la api key que nos dió el contenedor de docker que levanto la app.