# 5GNetwork_in_Manufacturing

Source Data: [5G Network Slicing in Quality Control Process of Manufacturing](reports/5G_in_Defect_Inspection_Facility_250506.pdf)

## Purpose

Simulate QOS Control process data transmission over 5G on kubernetes platform

## How To 

To use this repository you need to have access into a running kubernetes cluster and proceed with the following steps

## Deploy OAI 5G Core 

1. Deploy Core Network 
  ```bash
  cd helm-charts/oai-5g-core-basic
  helm dependency update
  helm install oai-5g-basic .
  ```

## Deploy OAI RAN: Sepparate CU & DU (F1 Mode)

1. Deploy CU
  ```bash
  cd helm-charts/oai-5g-ran/oai-du
  helm install oai-du .
  ```

2. Deploy DU
  ```bash
  cd helm-charts/oai-5g-ran/oai-du
  helm install oai-du .
  ```

