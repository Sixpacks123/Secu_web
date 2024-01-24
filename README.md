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
