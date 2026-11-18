# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :** Weistroffer Samuel
**Groupe :** RS2

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   >Le cluster affiche 3 nœuds : un Control Plane (k3d-lab-server-0) et deux Workers (k3d-lab-agent-0 et k3d-lab-agent-1). Les trois nœuds sont dans l'état Ready, ce qui signifie qu'ils sont opérationnels et peuvent participer au fonctionnement du cluster.
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   >La commande docker ps affiche les conteneurs Docker utilisés pour faire fonctionner le cluster k3d. On retrouve un conteneur pour le Control Plane, deux conteneurs pour les Workers et un conteneur supplémentaire pour le Load Balancer.

Dans k3d, un nœud Kubernetes est concrètement un conteneur Docker exécutant K3s. Chaque conteneur joue un rôle précis dans le cluster et permet de simuler une infrastructure Kubernetes composée de plusieurs machines.

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   >La commande kubectl create deployment web --image=nginx a créé un Deployment nommé web, qui a ensuite entraîné la création d'un ReplicaSet et d'un Pod contenant le conteneur Nginx.La commande kubectl expose deployment web --port=80 a également créé un Service de type ClusterIP permettant d'exposer l'application sur le port 80 à l'intérieur du cluster.
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   >Après la suppression du Pod, Kubernetes a automatiquement créé un nouveau Pod avec un nom différent (web-68d995574f-mjgfq). Cela s'explique par le fonctionnement du ReplicaSet, qui surveille en permanence le nombre de Pods disponibles et veille à maintenir le nombre de réplicas souhaité par le Deployment. Comme un seul Pod était demandé, Kubernetes en a automatiquement recréé un pour maintenir cet état.

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   >Non, le message « Bienvenue sur mon site Web » n'a pas survécu à la suppression du Pod. La modification avait été effectuée directement dans le système de fichiers du conteneur et n'était pas persistante. Lors de la suppression du Pod, Kubernetes en a créé un nouveau à partir de l'image Nginx originale. Le nouveau conteneur ne contenait donc pas les modifications effectuées précédemment.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   >Le port-forward a cessé de fonctionner parce qu'il était connecté à l'ancien Pod. Lorsque celui-ci a été supprimé, son environnement d'exécution a également disparu, ce qui a entraîné la perte de connexion. Kubernetes a bien recréé un nouveau Pod, mais le port-forward ne se reconnecte pas automatiquement à ce dernier. Il faut donc relancer la commande kubectl port-forward pour établir un nouveau tunnel.

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   >kubectl create est une commande impérative qui permet de créer directement une ressource Kubernetes en indiquant ses paramètres dans la commande.kubectl apply utilise une approche déclarative : on décrit l'état souhaité dans des fichiers YAML et Kubernetes crée ou met à jour les ressources pour correspondre à cette configuration.L'approche déclarative est particulièrement adaptée à la production, car les fichiers YAML peuvent être versionnés avec Git et réutilisés facilement.
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   >Le selector d'un Service permet d'identifier les Pods auxquels il doit transmettre le trafic grâce à leurs labels. Dans notre cas, le selector app: site permet au Service de retrouver les Pods possédant ce même label. Si aucun Pod ne correspond au selector, le Service existe toujours mais ne possède aucun endpoint. Il ne peut donc transmettre le trafic à aucune application.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```
@sweistroffer05 ➜ /workspaces/atelier-kubernetes (main) $ kubectl get pods -o wide -w
NAME                    READY   STATUS    RESTARTS   AGE     IP          NODE               NOMINATED NODE   READINESS GATES
site-7b6d9fff54-5v6vl   1/1     Running   0          6m47s   10.42.1.6   k3d-lab-server-0   <none>           <none>
web-68d995574f-5dhd4    1/1     Running   0          22m     10.42.2.6   k3d-lab-agent-0    <none>           <none>
site-7b6d9fff54-46sss   0/1     Pending   0          0s      <none>      <none>             <none>           <none>
site-7b6d9fff54-n4n6t   0/1     Pending   0          0s      <none>      <none>             <none>           <none>
site-7b6d9fff54-46sss   0/1     Pending   0          0s      <none>      k3d-lab-agent-1    <none>           <none>
site-7b6d9fff54-n4n6t   0/1     Pending   0          0s      <none>      k3d-lab-agent-0    <none>           <none>
site-7b6d9fff54-46sss   0/1     ContainerCreating   0          0s      <none>      k3d-lab-agent-1    <none>           <none>
site-7b6d9fff54-n4n6t   0/1     ContainerCreating   0          0s      <none>      k3d-lab-agent-0    <none>           <none>
site-7b6d9fff54-46sss   1/1     Running             0          1s      10.42.0.7   k3d-lab-agent-1    <none>           <none>
site-7b6d9fff54-n4n6t   1/1     Running             0          7s      10.42.2.7   k3d-lab-agent-0    <none>           <none>
site-7468f5cd4f-hqs28   0/1     Pending             0          0s      <none>      <none>             <none>           <none>
site-7468f5cd4f-hqs28   0/1     Pending             0          0s      <none>      k3d-lab-agent-1    <none>           <none>
site-7468f5cd4f-hqs28   0/1     ContainerCreating   0          0s      <none>      k3d-lab-agent-1    <none>           <none>
site-7468f5cd4f-hqs28   1/1     Running             0          5s      10.42.0.8   k3d-lab-agent-1    <none>           <none>
   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    >Le nouveau Pod avait le statut ErrImagePull, puis ImagePullBackOff. La commande kubectl describe pod  m'a permis d'identifier la cause dans la section Events : l'image nginx:9.99 est introuvable sur Docker Hub (not found). Kubernetes ne peut donc pas la télécharger.  
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    >Oui, le site est resté accessible pendant la panne. Kubernetes a conservé les anciens Pods fonctionnels puisque le nouveau Pod n'était pas prêt. Le Service a continué à distribuer le trafic vers les anciens Pods, assurant ainsi la continuité du service.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    >
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    >

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
