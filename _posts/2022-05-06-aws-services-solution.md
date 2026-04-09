---
permalink: /posts/aws_service_solution
display: normal
layout: post
date: '2022-05-06 21:58:28 +08:00'
title: AWS Services Solution
tags: AWS
mathjax: true
---
* content 
{:toc}
Use AWS Services, Amazon API Gateway, AWS Lambda, and Amazon DynamoDB to bulid serverless services







## Flow
![Solutions](https://raw.githubusercontent.com/q8977452/q8977452.github.io/master/uploads/api_gateway.png)

## Examples
- 轉縮圖程式
    - lambda
        - createThumnail
            - upload url
            - thumbnail url
    - api gateway
        - x-api-key
        - route
        - use custom domain
    - dynamodb
        - image
            - name
            - s3 url
                - upload
                - thumbnail

## Official Document
- [設定 REST API 的自訂網域名稱](https://docs.aws.amazon.com/zh_tw/apigateway/latest/developerguide/how-to-custom-domains.html)
- [使用 DynamoDB 和 Lambda 的無伺服器架構](https://pages.awscloud.com/GLOBAL-PAC-WEBINAR-AWS-OTT-FY21-08-24_2021_VW_s22e01-DAT_On-DemandVirtualWorkshop.html)
