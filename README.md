Container Security Project
Overview
This project demonstrates a container security and monitoring solution for a sample web application, implementing a CI/CD pipeline that performs static vulnerability scanning of container images before deployment and dynamic runtime monitoring for malicious activity. The project uses modern DevSecOps tools to showcase a "shift-left" security approach, integrating static analysis with real-time observability.
Project Goal: Build a simulated CI/CD pipeline that:

Scans container images (e.g., alpine:3.10) for vulnerabilities using static analysis tools like Trivy.
Monitors running containers for performance and security issues using cAdvisor and Prometheus.

Prerequisites

Docker: Container runtime for building and running images.
Minikube: Local Kubernetes cluster for deployment simulation.
Security Tools:
Trivy: Open-source vulnerability scanner for container images.
cAdvisor: Container Advisor for real-time container metrics.
Prometheus: Time-series database for monitoring and alerting.


Docker Compose: For orchestrating multi-container setups.
Basic Knowledge: Familiarity with Docker, Kubernetes, and PromQL.

Step-by-Step Guide
1. Install Docker

Install Docker on your system:sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker


Verify:docker --version



2. Install Minikube (Kubernetes Cluster)

Install Minikube for a local Kubernetes cluster:curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube


Start Minikube:minikube start


Verify:kubectl get nodes



3. Install Security Tools

Install Trivy:sudo apt-get install -y wget apt-transport-https
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt update
sudo apt install -y trivy


Verify:trivy --version



4. Static Vulnerability Scanning

Scan the alpine:3.10 image for vulnerabilities:trivy image alpine:3.10


Output: Lists CVEs and misconfigurations (e.g., outdated packages).
Screenshot: [Insert image1.png here, showing Trivy scan results for alpine:3.10]

5. Set Up Docker Compose for Monitoring

Create a docker-compose.yml file to define services for alpine-app, cAdvisor, and Prometheus.

version: '3'
services:
  alpine-app:
    image: alpine:3.10
    container_name: alpine-app
    command: sh -c "while true; do echo 'Activity log' >> /log.txt; sleep 10; done"
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    privileged: true
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:rw
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    ports:
      - "8080:8080"
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    depends_on:
      - cadvisor


Explanation:

alpine-app: Runs alpine:3.10, generating minimal activity (writes to log.txt) for monitoring.
cadvisor: Collects container metrics, requiring privileged access and host filesystem mounts (/var/run/docker.sock, etc.).
prometheus: Scrapes metrics from cAdvisor, configured via prometheus.yml.


Screenshot: [Insert image2.png here, showing Docker Compose setup or running containers]


6. Configure Prometheus

Create a prometheus.yml file for Prometheus configuration.

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']


Explanation:

global: Sets a 15-second scrape interval.
scrape_configs:
prometheus: Monitors Prometheus itself (port 9090).
cadvisor: Scrapes cAdvisor metrics (port 8080) for container data.




Start the stack:
docker-compose up -d


Verify Prometheus is running:
curl http://localhost:9090


Screenshot: [Insert image3.png here, showing Prometheus UI at http://localhost:9090]


7. Runtime Monitoring with PromQL

Access Prometheus UI at http://localhost:9090.
Run PromQL queries to monitor alpine-app:
CPU Usage (by service label):rate(container_cpu_usage_seconds_total{container_label_com_docker_compose_service="alpine-app"}[5m])


Measures CPU usage rate for alpine-app over 5 minutes.
Screenshot: [Insert image4.png here, showing CPU usage graph]


CPU Usage (by image):rate(container_cpu_usage_seconds_total{image="alpine:3.10"}[5m])


Measures CPU usage for the alpine:3.10 image.
Screenshot: [Insert image5.png here, showing CPU usage graph]


Memory Usage:container_memory_usage_bytes{container_label_com_docker_compose_service="alpine-app"}


Screenshot: [Insert image6.png here, showing memory usage]


Network I/O:rate(container_network_receive_bytes_total{container_label_com_docker_compose_service="alpine-app"}[5m])


Screenshot: [Insert image7.png here, showing network I/O]





8. Troubleshooting

Prometheus Not Scraping:
Verify cAdvisor:curl http://localhost:8080/metrics


Check prometheus.yml for correct targets.
Screenshot: [Insert image8.png here, showing cAdvisor metrics]


Docker Permissions:
Add user to Docker group:sudo usermod -aG docker $USER




Minikube Issues:
Restart Minikube:minikube stop && minikube start





9. Integration with Ethical Hacking

Vulnerability Scanning: Trivy results identify CVEs in alpine:3.10 (e.g., outdated libraries).
Runtime Monitoring: PromQL queries detect anomalies (e.g., unexpected CPU spikes).
Lab Context: Aligns with prior tasks (e.g., Censys.io, Google Dorking on hackthebox.com):
Scan container images for CVEs before deployment, similar to Censys certificate checks.
Monitor runtime behavior, complementing Shodan port scans.
Screenshot: [Insert image9.png here, showing Trivy CVE report]



Project Summary
This project engineered a robust container security and monitoring solution for a sample web application, demonstrating expertise in static and dynamic analysis. Key contributions include:

Container Vulnerability Management:
Conducted static analysis of alpine:3.10 using Trivy, identifying CVEs and misconfigurations.
Screenshot: [Insert image10.png here, showing Trivy results]


Dynamic Runtime Monitoring:
Deployed a multi-container stack with Docker Compose, including:
Prometheus: Central data store for metrics.
cAdvisor: Real-time container performance metrics.
PromQL Queries: Analyzed CPU, memory, and network I/O.
Screenshots: [Insert image11.png, image12.png for Prometheus dashboards]




Problem-Solving:
Resolved issues like Prometheus scraping failures due to incorrect labels.
Screenshot: [Insert image13.png here, showing resolved scraping issue]


Technical Proficiency:
Demonstrated skills in Docker Compose, Prometheus, cAdvisor, and Trivy.
Showcased a holistic understanding of container lifecycle security.



Usage

Clone this repository:git clone <repository-url>
cd <repository-directory>


Run Docker Compose:docker-compose up -d


Access Prometheus: http://localhost:9090
Run Trivy scans:trivy image alpine:3.10



Contributing
Contributions are welcome! Please submit pull requests or open issues for suggestions.
License
This project is licensed under the MIT License.
