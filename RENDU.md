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
   >
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   >

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   >
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   >

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```

   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    >
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    >

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
