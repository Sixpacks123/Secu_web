
> MariaDB not working at the moment

# Docker Swarm avec Réplicas - README

Ce projet vise à créer un environnement Docker Swarm avec plusieurs réplicas d'un service pour garantir la haute disponibilité 

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
