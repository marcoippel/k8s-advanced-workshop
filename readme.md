# Voer de volgende stappen eerst uit voor je begint

## Install nginx controller in Docker Desktop

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.6.4/deploy/static/provider/aws/deploy.yaml
```

## Open de hostfile op je computer en voeg hier de volgende regel aan toe

```
127.0.0.1 wordpress.cloudrepublic.internal
```

## Stappenplan

1. Maak een namespace aan.
2. Maak een pvc aan voor de mysql deployment
3. Maak een pvc aan voor de wordpress bestanden mount deze op pad "/var/www/html"
4. Maak een secret aan voor de SA user van de MySql deployment
5. Maak een Wordpress deployment aan
6. Maak een MySql deployment aan
7. Maak de services aan voor de Wordpress en Mysql deployments
8. Maak een ingress aan met de url wordpress.cloudrepublic.internal
10. Open de url wordpress.cloudrepublic.internal als het goed is zie je nu de Wordpress setup page
11. Maak een Role aan welke alleen rechten heeft om de pods en deplouments in de door jou aangemaakte namespace te zien

```

<!-- Set the credentials -->
kubectl config set-credentials pod-deployment-viewer --token=$(kubectl describe secret -n study $(kubectl get secret -n study | Select-String "pod-deployment-viewer-account" | ForEach-Object { $_.Line.Split(' ', [StringSplitOptions]::RemoveEmptyEntries)[0] }) | Select-String "token:" | ForEach-Object { $_.Line.Split(':      ')[1] })

<!-- Set the context -->
kubectl config set-context pod-deployment-viewer-context --user=pod-deployment-viewer --namespace=study

<!-- Set the cluster -->
kubectl config set-context pod-deployment-viewer-context --cluster=docker-desktop --user=pod-deployment-viewer --namespace=study

<!-- Use the context -->
kubectl config use-context pod-deployment-viewer-context

<!-- Reset the credentials -->
kubectl config set-context docker-desktop --user=docker-desktop --namespace=study
kubectl config use-context docker-desktop
```

## Troubleshooting

- Ingress werkt niet:
  - luistert er niets op poort 80

    ```
    netstat -ano | findstr ":80" | findstr "LISTENING"
    ```

- Inloggen werkt niet met het account pod-deployment-viewer
  - kijk naar de kubeconfig of deze goed staat en of er een token in zit
