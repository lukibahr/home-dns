# Home DNS

Home DNS is a tiny project to test custom DNS entries

## Testing your DNS

To test your configuration, you'll likely run the docker container

```bash
$ docker run -it --name coredns --rm -v $(pwd):/root/ -p 53:53/udp -p 8080:8080 -p 9153:9153 coredns/coredns -conf /root/Corefile
k3s.local.:53
.:53
2019-06-13T14:44:05.732Z [INFO] CoreDNS-1.5.0
2019-06-13T14:44:05.732Z [INFO] linux/amd64, go1.12.2, e3f9a80
CoreDNS-1.5.0
linux/amd64, go1.12.2, e3f9a80
```
Next, you can test your preconfigured DNS resolution with the `dig` command:
`dig @localhost web.k3s.local`

which should result in the following log output: 

```bash
2019-06-13T14:44:52.452Z [INFO] 172.17.0.1:54322 - 1005 "A IN web.k3s.local. udp 54 false 4096" NOERROR qr,aa,rd 60 0.0001037s
```

## It's all about the metrics and a healthy service

If you want to be aware wha'ts going on on your tiny dns services you should consider the builtin prometheus metrics. Set up the config with the health and prometheus plugin: 

```config

k3s.local:53 {
    file /root/local.db
    log
    health
    prometheus :9153
    errors
    reload 30s
    cache 30
    loop
}
```
and query the prometheus metrics endpoint `wget -SO- http://localhost:9153/metrics`. The health endpoint can be activated with the `health` directive and queried with the following HTTP request: `$ wget http://localhost:8080/health`

## Finalize

This home-dns setup is useful for personal needs. If you're working with a local minikube or whatever-"netes" setup and you have enough of changing your etc/hosts file, this home dns setup comes in play. Optionally, you could run it in background and automate the creation of dns entries in your `.db` file.

