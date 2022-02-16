---
layout: default
title: DomainDeploy
parent: General
nav_order: 1
has_children: false
---


# Domain Deploy

[git](https://github.com/Viva-con-Agua/domain-deploy.git)    

## Setup

Get repo

```
 git clone https://github.com/Viva-con-Agua/domain-deploy.git
```

Create folder named `subdomains` and change to the repo root directory.
```
mkdir subdomains
cd domain-deploy
```
Create a new `.env` file and edit:

```
cp example.env .env

subdomains=/path/to/subdomain
cert=/path/to/cert
 ``` 

## Setup Network
 ```
  please network create
  please network info
  please network delete
 ```

## Setup Domain

```
 ./please setup
```

## Usage

```
Usage: 
 up                           # setup nginx
 rm                           # remove nginx
 logs                         # show nginx logs
 restart                      # restart nginx
 add <subdomain> <domain>     # add subdomain
 link <subdomain>             # connect subdomain with service
 edit <subdomain>             # edit subdomain
 help                         # print help
```

### Add subdomain

Add subdomain to the services.ini file.
    ```
        ./please add test example.com
    ```

### Link subdomain
Link service
    ```
    ./please link test
    ```
Output:
    ```
        subdomain test.example.com is successfully link to docker with IP: 172.4.0.4
    ```

Use the IP as `domain_ip` in your service.

