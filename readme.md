# Voer de volgende stappen eerst uit voor je begint

## Install k9s

```
choco install k9s
```

## Install nginx controller in Docker Desktop

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0-beta.0/deploy/static/provider/cloud/deploy.yaml
```

## Open de hostfile op je computer en voeg hier de volgende regel aan toe

```
127.0.0.1 wordpress.cloudrepublic.internal
```

## Stappenplan

1. Maak een namespace aan.
2. Maak een pvc aan voor de mysql deployment
3. Maak een pvc aan voor de wordpress bestanden mount deze op pad "/var/www/html"
4. Maak een secret aan voor de Admin user van de MySql deployment
5. Maak een Wordpress deployment aan
6. Maak een MySql deployment aan
7. Maak de services aan voor de Wordpress en Mysql deployments
8. Maak een ingress aan met de url wordpress.cloudrepublic.internal
9. Maak een database aan in de my sql

  ```
  kubectl get pods -n wordpress -l tier=mysql
  kubectl exec -it -n wordpress POD_NAME -- mysql -u root -p
  CREATE DATABASE IF NOT EXISTS wordpress;
  ```

10. Open de url wordpress.cloudrepublic.internal als het goed is zie je nu de Wordpress setup page

## Troubleshooting

- Ingress werkt niet:
  - luistert er niets op poort 80

    ```
    netstat -ano | findstr ":80" | findstr "LISTENING"
    ```

- Inloggen werkt niet met het account pod-deployment-viewer
  - kijk naar de kubeconfig of deze goed staat en of er een token in zit

- Geen verbinding met de database:
  - kubectl get pods -n wordpress -l tier=mysql
  - kubectl exec -it -n wordpress POD_NAME -- mysql -u root -p

- Installeer ping
  - apt-get update -y
  - apt-get install -y iputils-ping

- Instaleer vim
  - apt-get update
  - apt-get install vim
