
> MariaDB not working at the moment

# Docker Swarm avec Réplicas - README

Ce projet vise à créer un environnement Docker Swarm avec plusieurs réplicas d'un service pour garantir la haute disponibilité 
[![](https://mermaid.ink/img/pako:eNp9kU9rhDAQxb9KmFMKu4X26KGgCF50EVpvucya0c2iieQPtCz73Rtri7YF55Rf3rzHC7lBayRBAr3F6cLecqFZHBfOy0U6TYNq0SujF2WelJ-i6fHqZpk9PaxK9kt5_lZIS6H_BJcGJctwQN2SXQNKfuqVft8x5ujxjI5WT8UrtArzbMdVGa28sUr3q6_mtTUj-QsFt3lDwQuLHWrciWvctnTDZ_6_nrLj8YWVC2RbKL-g2kK9QLGF5scDBxjJjqhk_KrbLAqIvUcSkMSjpA7D4AUIfY-rGLx5_dAtJN4GOkCYJHrKFcbyIyQdDo7unzVPkyQ?type=png)](https://mermaid.live/edit#pako:eNp9kU9rhDAQxb9KmFMKu4X26KGgCF50EVpvucya0c2iieQPtCz73Rtri7YF55Rf3rzHC7lBayRBAr3F6cLecqFZHBfOy0U6TYNq0SujF2WelJ-i6fHqZpk9PaxK9kt5_lZIS6H_BJcGJctwQN2SXQNKfuqVft8x5ujxjI5WT8UrtArzbMdVGa28sUr3q6_mtTUj-QsFt3lDwQuLHWrciWvctnTDZ_6_nrLj8YWVC2RbKL-g2kK9QLGF5scDBxjJjqhk_KrbLAqIvUcSkMSjpA7D4AUIfY-rGLx5_dAtJN4GOkCYJHrKFcbyIyQdDo7unzVPkyQ)
## Fonctionnement de l'Environnement

1. **Docker Swarm** : Docker Swarm est un orchestrateur de conteneurs qui permet de gérer et de déployer des conteneurs sur un cluster de nœuds. Il offre des fonctionnalités telles que la mise à l'échelle automatique et la répartition de la charge.

2. **Services et Réplicas** : Nous utilisons Docker Swarm pour créer un service qui définit comment notre application doit être exécutée. Dans ce service, nous spécifions le nombre de réplicas que nous souhaitons exécuter. Chaque réplica est une instance de notre application.

3. **Planificateur Swarm** : Le planificateur Swarm est responsable de la répartition des réplicas sur les nœuds du cluster. Il prend en compte la répartition de la charge, les ressources disponibles et d'autres critères pour décider où exécuter chaque réplica.

4. **Redondance et Disponibilité** : Les réplicas garantissent la redondance et la disponibilité de l'application. Si l'une des réplicas tombe en panne ou devient inaccessible, Docker Swarm crée automatiquement une nouvelle réplica sur un autre nœud pour maintenir le nombre souhaité.

5. **Équilibrage de Charge** : Docker Swarm gère automatiquement l'équilibrage de charge entre les réplicas. Les demandes sont réparties entre les différentes réplicas du service pour garantir une répartition équilibrée de la charge.

## Technologies Utilisées

- **Docker Swarm** : Pour l'orchestration de conteneurs.
- **Docker Compose** : Pour la définition de l'environnement dans le fichier `docker-compose.yml`.
- **Nginx** : Pour le serveur proxy et l'équilibrage de charge.
- **Prometheus** : Pour la surveillance et la collecte de métriques.
- **Grafana** : Pour la visualisation des métriques et la création de tableaux de bord.
- **MariaDB** : Pour la base de données.
- **Node.js** : Pour l'application de saisie et de lecture de formulaires.

## Exécution du Projet

1. Build the Node.js app image:

   ```bash
   cd node_js
   docker build -t test_nodejs_app1 .
   ```

2. Pull any required Docker images for your services:

   ```bash
   docker pull mariadb:latest
   docker pull prom/prometheus:v2.20.1
   docker pull grafana/grafana:latest
   docker pull nginx:latest
   ```

3. Navigate to your Swarm configuration directory:

   ```bash
   cd swarm_configs
   ```

4. Deploy your Docker stack using the provided `docker-stack.yml` file:

   ```bash
   docker stack deploy -c docker-stack.yml dev
   ```

5. Verify that your services are running:

   ```bash
   docker service ls
   ```


# MariDb config
### Sur le Serveur Principal (Master) :

1. **Connectez-vous en tant qu'administrateur** :

   Assurez-vous d'être connecté en tant qu'administrateur (`root` ou un utilisateur avec des privilèges administratifs) sur le serveur principal.

2. **Accordez les Privilèges de Réplication** :

   Accordez les privilèges de réplication à un utilisateur spécifique qui sera utilisé par le serveur secondaire pour se connecter au serveur principal. Remplacez `replica` et `replica_pass` par les valeurs de votre choix :

   ```sql
   GRANT REPLICATION SLAVE ON *.* TO 'replica'@'%' IDENTIFIED BY 'replica_pass';
   FLUSH PRIVILEGES;
   ```

3. **Obtenez les Informations de Réplication** :

   Exécutez la commande suivante pour obtenir les informations nécessaires à la configuration de la réplication sur le serveur secondaire. Vous aurez besoin du nom du fichier log binaire (Binary Log File) et de la position du log binaire (Position in Log File) :

   ```sql
   SHOW MASTER STATUS;
   ```

   Prenez note du nom du fichier log binaire (par exemple, `mysql-bin.000001`) et de la position du log (par exemple, `123`).

### Sur le Serveur Secondaire (Slave) :

1. **Connectez-vous en tant qu'administrateur** :

   Connectez-vous en tant qu'administrateur (`root` ou un utilisateur avec des privilèges administratifs) sur le serveur secondaire.

2. **Configurez la Réplication** :

   Utilisez les informations obtenues précédemment pour configurer la réplication sur le serveur secondaire en utilisant la commande `CHANGE MASTER TO`. Assurez-vous de remplacer `master_host`, `master_user`, `master_password`, `master_log_file`, et `master_log_pos` par les valeurs appropriées :

   ```sql
   CHANGE MASTER TO
     MASTER_HOST = 'adresse_ip_ou_nom_du_serveur_principal',
     MASTER_USER = 'replica',
     MASTER_PASSWORD = 'replica_pass',
     MASTER_LOG_FILE = 'nom_du_fichier_log_binaire',
     MASTER_LOG_POS = position_du_log;
   ```

   Par exemple :

   ```sql
   CHANGE MASTER TO
     MASTER_HOST = '192.168.1.100',
     MASTER_USER = 'replica',
     MASTER_PASSWORD = 'replica_pass',
     MASTER_LOG_FILE = 'mysql-bin.000001',
     MASTER_LOG_POS = 123;
   ```

3. **Démarrage de la Réplication** :

   Démarrez la réplication sur le serveur secondaire avec la commande suivante :

   ```sql
   START SLAVE;
   ```

4. **Vérifiez l'État de la Réplication** :

   Pour vérifier que la réplication fonctionne correctement, exécutez la commande suivante :

   ```sql
   SHOW SLAVE STATUS\G;
   ```

   Assurez-vous de rechercher l'état `Slave_IO_Running` et `Slave_SQL_Running`. Ces valeurs doivent être définies sur `Yes` pour indiquer que la réplication est en cours d'exécution.

Une fois ces étapes effectuées, la réplication maître-esclave devrait être configurée et fonctionnelle entre le serveur principal (master) et le serveur secondaire (slave). Les données du serveur principal seront répliquées sur le serveur secondaire. Assurez-vous de surveiller régulièrement l'état de la réplication pour vous assurer qu'elle fonctionne correctement.
