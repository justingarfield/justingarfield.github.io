## See all the things

```shell
talosctl get rd --nodes 192.168.60.9 --insecure | grep -v "resourcedefinitions.meta.cosi.dev"
```

```shell
NODE   NAMESPACE   TYPE                 ID                                               VERSION   ALIASES
       meta        ResourceDefinition   addressspecs.net.talos.dev                       1         addressspec as
       meta        ResourceDefinition   addressstatuses.net.talos.dev                    1         address addresses addressstatus as
       meta        ResourceDefinition   admissioncontrolconfigs.kubernetes.talos.dev     1         admissioncontrolconfig acc accs
       meta        ResourceDefinition   affiliates.cluster.talos.dev                     1         affiliate
       meta        ResourceDefinition   apicertificates.secrets.talos.dev                1         apicertificate ac acs
       meta        ResourceDefinition   apiserverconfigs.kubernetes.talos.dev            1         apiserverconfig apisc apiscs
       meta        ResourceDefinition   auditpolicyconfigs.kubernetes.talos.dev          1         auditpolicyconfig apc apcs
       meta        ResourceDefinition   bootstrapmanifestsconfigs.kubernetes.talos.dev   1         bootstrapmanifestsconfig bmc bmcs
       meta        ResourceDefinition   certsans.secrets.talos.dev                       1         certsan csan csans
       meta        ResourceDefinition   configstatuses.kubernetes.talos.dev              1         configstatus cs
       meta        ResourceDefinition   controllermanagerconfigs.kubernetes.talos.dev    1         controllermanagerconfig cmc cmcs
       meta        ResourceDefinition   cpustats.perf.talos.dev                          1         cpustat cpus
       meta        ResourceDefinition   deviceconfigspecs.net.talos.dev                  1         deviceconfigspec dcs
       meta        ResourceDefinition   discoveryconfigs.cluster.talos.dev               1         discoveryconfig dc dcs
       meta        ResourceDefinition   endpoints.kubernetes.talos.dev                   1         endpoint
       meta        ResourceDefinition   etcdconfigs.etcd.talos.dev                       1         etcdconfig ec ecs
       meta        ResourceDefinition   etcdmembers.etcd.talos.dev                       1         etcdmember em ems
       meta        ResourceDefinition   etcdrootsecrets.secrets.talos.dev                1         etcdrootsecret ers
       meta        ResourceDefinition   etcdsecrets.secrets.talos.dev                    1         etcdsecret es
       meta        ResourceDefinition   etcdspecs.etcd.talos.dev                         1         etcdspec es
       meta        ResourceDefinition   etcfilespecs.files.talos.dev                     1         etcfilespec efs
       meta        ResourceDefinition   etcfilestatuses.files.talos.dev                  1         etcfilestatus efs
       meta        ResourceDefinition   extensionstatuses.runtime.talos.dev              1         extensions extensionstatus es
       meta        ResourceDefinition   extramanifestsconfigs.kubernetes.talos.dev       1         extramanifestsconfig emc emcs
       meta        ResourceDefinition   hardwareaddresses.net.talos.dev                  1         hardwareaddress ha has
       meta        ResourceDefinition   hostnamespecs.net.talos.dev                      1         hostnamespec hs
       meta        ResourceDefinition   hostnamestatuses.net.talos.dev                   1         hostname hostnamestatus hs
       meta        ResourceDefinition   identities.cluster.talos.dev                     1         identity
       meta        ResourceDefinition   infos.cluster.talos.dev                          1         info
       meta        ResourceDefinition   kernelmodulespecs.runtime.talos.dev              1         modules kernelmodulespec kms
       meta        ResourceDefinition   kernelparamdefaultspecs.runtime.talos.dev        1         kernelparamdefaultspec kpds
       meta        ResourceDefinition   kernelparamspecs.runtime.talos.dev               1         kernelparamspec kps
       meta        ResourceDefinition   kernelparamstatuses.runtime.talos.dev            1         sysctls kernelparameters kernelparams kernelparamstatus kps
       meta        ResourceDefinition   kubeletconfigs.kubernetes.talos.dev              1         kubeletconfig kc kcs
       meta        ResourceDefinition   kubeletlifecycles.kubernetes.talos.dev           1         kubeletlifecycle kl kls
       meta        ResourceDefinition   kubeletsecrets.secrets.talos.dev                 1         kubeletsecret ks
       meta        ResourceDefinition   kubeletspecs.kubernetes.talos.dev                1         kubeletspec ks
       meta        ResourceDefinition   kubernetesaccessconfigs.cluster.talos.dev        1         kubernetesaccessconfig kac kacs
       meta        ResourceDefinition   kubernetesdynamiccerts.secrets.talos.dev         1         kubernetesdynamiccert kdc kdcs
       meta        ResourceDefinition   kubernetesrootsecrets.secrets.talos.dev          1         kubernetesrootsecret krs
       meta        ResourceDefinition   kubernetessecrets.secrets.talos.dev              1         kubernetessecret ks
       meta        ResourceDefinition   kubespanconfigs.kubespan.talos.dev               1         kubespanconfig ksc kscs
       meta        ResourceDefinition   kubespanendpoints.kubespan.talos.dev             1         kubespanendpoint kse kses
       meta        ResourceDefinition   kubespanidentities.kubespan.talos.dev            1         kubespanidentity ksi ksis
       meta        ResourceDefinition   kubespanpeerspecs.kubespan.talos.dev             1         kubespanpeerspec ksps
       meta        ResourceDefinition   kubespanpeerstatuses.kubespan.talos.dev          1         kubespanpeerstatus ksps
       meta        ResourceDefinition   linkrefreshes.net.talos.dev                      1         linkrefresh lr lrs
       meta        ResourceDefinition   linkspecs.net.talos.dev                          1         linkspec ls
       meta        ResourceDefinition   linkstatuses.net.talos.dev                       1         link links linkstatus ls
       meta        ResourceDefinition   machineconfigs.config.talos.dev                  1         machineconfig mc mcs
       meta        ResourceDefinition   machinestatuses.runtime.talos.dev                1         machinestatus ms
       meta        ResourceDefinition   machinetypes.config.talos.dev                    1         machinetype mt mts
       meta        ResourceDefinition   manifests.kubernetes.talos.dev                   1         manifest
       meta        ResourceDefinition   manifeststatuses.kubernetes.talos.dev            1         manifeststatus ms
       meta        ResourceDefinition   members.cluster.talos.dev                        1         member
       meta        ResourceDefinition   memorymodules.hardware.talos.dev                 1         memorymodules ram memorymodule mm mms
       meta        ResourceDefinition   memorystats.perf.talos.dev                       1         memorystat ms
       meta        ResourceDefinition   metakeys.runtime.talos.dev                       1         meta metakey mk mks
       meta        ResourceDefinition   mountstatuses.runtime.talos.dev                  1         mounts mountstatus ms
       meta        ResourceDefinition   namespaces.meta.cosi.dev                         1         ns namespace
       meta        ResourceDefinition   networkstatuses.net.talos.dev                    1         netstatus netstatuses networkstatus ns
       meta        ResourceDefinition   nodeaddresses.net.talos.dev                      1         nodeaddress na nas
       meta        ResourceDefinition   nodeaddressfilters.net.talos.dev                 1         nodeaddressfilter naf nafs
       meta        ResourceDefinition   nodeipconfigs.kubernetes.talos.dev               1         nodeipconfig nipc nipcs
       meta        ResourceDefinition   nodeips.kubernetes.talos.dev                     1         nodeip nip nips
       meta        ResourceDefinition   nodelabelspecs.k8s.talos.dev                     1         nodelabelspec nls
       meta        ResourceDefinition   nodenames.kubernetes.talos.dev                   1         nodename
       meta        ResourceDefinition   operatorspecs.net.talos.dev                      1         operatorspec os
       meta        ResourceDefinition   osrootsecrets.secrets.talos.dev                  1         osrootsecret osrs
       meta        ResourceDefinition   pkistatuses.etcd.talos.dev                       1         pkistatus pkis
       meta        ResourceDefinition   platformmetadatas.talos.dev                      1         platformmetadata pm pms
       meta        ResourceDefinition   probespecs.net.talos.dev                         1         probespec ps
       meta        ResourceDefinition   probestatuses.net.talos.dev                      1         probe probes probestatus ps
       meta        ResourceDefinition   processors.hardware.talos.dev                    1         cpus cpu processor
       meta        ResourceDefinition   resolverspecs.net.talos.dev                      1         resolverspec rs
       meta        ResourceDefinition   resolverstatuses.net.talos.dev                   1         resolvers resolverstatus rs
       meta        ResourceDefinition   routespecs.net.talos.dev                         1         routespec rs
       meta        ResourceDefinition   routestatuses.net.talos.dev                      1         route routes routestatus rs
       meta        ResourceDefinition   schedulerconfigs.kubernetes.talos.dev            1         schedulerconfig sc scs
       meta        ResourceDefinition   seccompprofiles.cri.talos.dev                    1         seccompprofile sp sps
       meta        ResourceDefinition   secretstatuses.kubernetes.talos.dev              1         secretstatus ss
       meta        ResourceDefinition   services.v1alpha1.talos.dev                      1         svc service
       meta        ResourceDefinition   siderolinkconfigs.siderolink.talos.dev           1         siderolinkconfig sc scs
       meta        ResourceDefinition   staticpods.kubernetes.talos.dev                  1         staticpod sp sps
       meta        ResourceDefinition   staticpodserverstatuses.kubernetes.talos.dev     1         staticpodserverstatus spss
       meta        ResourceDefinition   staticpodstatuses.kubernetes.talos.dev           1         podstatus staticpodstatus sps
       meta        ResourceDefinition   systeminformations.hardware.talos.dev            1         systeminformation systeminformation si sis
       meta        ResourceDefinition   timeserverspecs.net.talos.dev                    1         timeserverspec tss
       meta        ResourceDefinition   timeserverstatuses.net.talos.dev                 1         timeserver timeservers timeserverstatus tss
       meta        ResourceDefinition   timestatuses.v1alpha1.talos.dev                  1         timestatus ts
       meta        ResourceDefinition   trustdcertificates.secrets.talos.dev             1         trustdcertificate tc tcs
```

## Let's call everything and see what's behind it

```shell
talosctl get rd --nodes 192.168.60.11 --insecure \
    | tail -n +2 \
    | grep -v "resourcedefinitions.meta.cosi.dev" \
    | awk '{print $3}' \
    | xargs -I % sh -c 'talosctl get % --nodes 192.168.60.11 --insecure;'
```

```shell
NODE   NAMESPACE   TYPE   ID   VERSION
1 error occurred:
        * rpc error: code = PermissionDenied desc = not authorized
```

As you can see, only certain endpoints are available using the `--insecure` flag, even while in maintenance mode.

## Filter out the PermissionDenied endpoints

```shell
talosctl get rd --nodes 192.168.60.11 --insecure \
    | tail -n +2 \
    | grep -v "resourcedefinitions.meta.cosi.dev" \
    | awk '{print $3}' \
    | xargs -I % sh -c 'talosctl get % --nodes 192.168.60.11 --insecure 1>/dev/null 2>/dev/null; if [ $? -eq 0 ]; then printf "%\n"; fi'
```

## Filter out blank responses

```shell
talosctl get rd --nodes 192.168.60.11 --insecure \
    | tail -n +2 \
    | grep -v "resourcedefinitions.meta.cosi.dev" \
    | awk '{print $3}' \
    | xargs -I % sh -c 'talosctl get % --nodes 192.168.60.11 --insecure 1>/dev/null 2>/dev/null; if [ $? -eq 0 ]; then talosctl get % --nodes 192.168.60.11 --insecure; fi | if [ $(wc -l) -gt 1 ]; then printf "%\n"; fi'
```

## Pull all the available things

```shell
talosctl get rd --nodes 192.168.60.11 --insecure \
    | tail -n +2 \
    | grep -v "resourcedefinitions.meta.cosi.dev" \
    | awk '{print $3}' \
    | xargs -I % sh -c 'talosctl get % --nodes 192.168.60.11 --insecure 1>/dev/null 2>/dev/null; if [ $? -eq 0 ]; then talosctl get % --nodes 192.168.60.11 --insecure; fi | if [ $(wc -l) -gt 1 ]; then talosctl get % --nodes 192.168.60.11 --insecure; fi'
```
