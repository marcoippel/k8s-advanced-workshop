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
11. Maak een Role aan welke alleen rechten heeft om de pods en deployments in de door jou aangemaakte namespace te zien

## Inloggen met een service account

1. Maak een service account aan met de naam: 'pod-deployment-viewer-account'
2. Maak een secret aan zoals omschreven in <https://kubernetes.io/docs/concepts/configuration/secret/#service-account-token-secrets> als er geen secret bestaat voor het service account.
3. Open het zojuist aangemaakte secret doormiddel van k9s (met x decode je het secret)
4. Voer het volgende commando uit om het service account toe te voegen aan de kubectl config: 'kubectl config set-credentials pod-deployment-viewer --token={het token uit stap 3}'
5. Koppel een context aan een user en een namespace in je kubeconfig file: kubectl config set-context pod-deployment-viewer-context --user=pod-deployment-viewer --namespace=study
6. Koppel het cluster aan de context: kubectl config set-context pod-deployment-viewer-context --cluster=docker-desktop --user=pod-deployment-viewer --namespace=study
7. Nu ben je ingelogd onder het service account en kan je alleen de resources zien welke opgegeven zijn bij het aanmaken van de role in het stappenplan punt 11.

## Om de credentials te resetten en om weer met de orginele gebruiker in te loggen

kubectl config set-context docker-desktop --user=docker-desktop --namespace=study
kubectl config use-context docker-desktop

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
