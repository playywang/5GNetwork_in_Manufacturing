# 5GNetwork_in_Manufacturing

Source Data: [5G Network Slicing in Quality Control Process of Manufacturing](reports/5G_in_Defect_Inspection_Facility_250506.pdf)

## Purpose

Simulate QOS Control process data transmission over 5G on kubernetes platform

## How To Setup The Testing Environment

To use this repository you need to have access into a running kubernetes cluster and proceed with the following steps

![Topology](https://ptuml.hackmd.io/svg/bLHRRzem57vUVeNB9h59TJFIFLKCbK6HAfMiQfIzT7rmOmCMYHtP9wkwgVzznt4kjC2ayH0SVvTpoR4lBJ03LP47mnFOY48GiZAw81k0qbw4eM5FPsi9c-gnii9aMe5GS9Rf8YnpffomNnJCWZ1XRME3uV24YvLKWan_LqRS8kC_NAa5dil7iDRIMxURBcz28tB1h0WZ6eLSMWW2p5kgBA-ug4pUX_B2cIuAhSvANr_3so4XNbOWF7bQbeamlsGc6i4HvrUYHQKoa6qCpCPkfIgPOGN9iNPuBWKfTVxiNe809EI290AgabmfJa5J_0k2NAo0W2P6hZT0k3IYDWrwC-nDgHLsAO5dj704fjOvUcRCS3BuiQJHWFm92FxmqU5ay7rwJQVBnO0mIpIJkDp7anvF3k7fROVZ4l4k7O_7TnsEomFnqwHF0DS78ioNNOJvSX-VnoqyZziYyV7IrtynqqRu5cJIPArGcVIAZ6gnMuwyT8yHTOo87A6STvHppoXPjcLhf2X3hLv1eRae6bA_KMVqfffPsbOpIn5zsJ6xMoJNorhpxasJ4P55gwjV3f3cdVFSEplluTqqTWdx1j2Fdp_HpeDSaAaqtioN_s_y_2YEX6vpJEjHffCka_eVz_tMB4NTOkGqG_VTI2pWIPkj9-AC4khE16qc4id9jz4EoFMJeZlJrD36NZLf1XLbyHUFpbpiMZLfJH0S7GGRPUH176X2g_AUCxiH_87sX-W8w4_84J1vfNGnNnjtvHp6_TbeSXpJ-_UFUILEkGOG_1GsCjigOUtNwA7fCnuMr-ATyHj_U7FPJNwgQxn_3LvMzUYuMmir5hIfXtiVRBvcJ5mGltCf57SNu5y0)

### Deploy OAI 5G Core 

1. Deploy Core Network 
  ```bash
  cd helm-charts/oai-5g-core-basic
  helm dependency update
  helm install oai-5g-basic .
  ```

### Deploy OAI RAN: Sepparate CU & DU (F1 Mode)

1. Deploy CU
  ```bash
  cd helm-charts/oai-5g-ran/oai-du
  helm install oai-du .
  ```
2. CU Config Values
```

config:
  timeZone: "Europe/Paris"
  useAdditionalOptions: "--sa --log_config.global_log_options level,nocolor,time"
  # If mounting the configuration file then below parameters are not used
  cuName: "oai-cu"
  mcc: "001"   # check the information with AMF, SMF, UPF
  mnc: "01"    # check the information with AMF, SMF, UPF
  tac: "1"     # check the information with AMF
  sst: "1"     
  amfhost: "oai-amf"  # amf ip-address or service-name oai-amf-svc or 172.21.6.94
  n2IfName: "eth0"    # if multus.n2Interface.create is true then use n2
  n3IfName: "eth0"   #if multus.n3Interface.create is true then use n3 or you can only use 1 interface n2 or eth0 
  f1IfName: "eth0"   #if multus.f1Interface.create is true then use multus.f1Interface.Ipadd
  f1cuPort: "2153"   #2153 if using same interface for f1 and n3 else standard port 2152 should be use if f1 and n3 interface are different

```


2. Deploy DU
  ```bash
  cd helm-charts/oai-5g-ran/oai-du
  helm install oai-du .
  ```
3. DU Config Values
```
config:
  timeZone: "Europe/Paris"
  useAdditionalOptions: "--sa --rfsim --log_config.global_log_options level,nocolor,time"
  duName: "oai-du-rfsim"
  mcc: "001"   # check the information with AMF, SMF, UPF
  mnc: "01"    # check the information with AMF, SMF, UPF
  tac: "1"     # check the information with AMF
  sst: "1"  #currently only 4 standard values are allowed 1,2,3,4
  usrp: rfsim   #allowed values rfsim, b2xx, n3xx or x3xx
  f1IfName: "eth0"   #if multus.f1Interface.create is true then use f1
  cuHost: "oai-cu" ## Ip-address or hostname
  f1cuPort: "2153"   #2153 if using same interface for f1 and n3 else standard port 2152 should be use if f1 and n3 interface are different
  f1duPort: "2153"   #2153 if using same interface for f1 and n3 else standard port 2152 should be use if f1 and n3 interface are different
```

### Deploy OAI nr-UE

1. Deploy OAI nr-UE

```bash
cd helm-charts/oai-5g-ran/oai-nr-ue
helm install oai-ne-ue .
```

2. Default Params

```
...
config:
  timeZone: "Europe/Paris"
  rfSimServer: "oai-ran"    # ip-address of rfsim or service name oai-gnb or oai-du
  fullImsi: "001010000000100"       # make sure all the below entries are present in the subscriber database
  fullKey: "fec86ba6eb707ed08905757b1bb44b8f"
  opc: "C42449363BBAD02B66D16BC975D77CC1"
  dnn: "oai"
  sst: "1"                     # configure according to gnb and amf, smf and upf 
  sd: "16777215"
  usrp: "rfsim"            # allowed rfsim, b2xx, n3xx, x3xx
  useAdditionalOptions: "--sa --rfsim -r 106 --numerology 1 -C 3619200000 --log_config.global_log_options level,nocolor,time"
...
```
