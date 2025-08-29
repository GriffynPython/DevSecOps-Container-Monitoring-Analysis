# DevSecOps-Container-Monitoring-Analysis
Overview

This project demonstrates a container security and monitoring solution for a sample web application, implementing a CI/CD pipeline that performs static vulnerability scanning of container images before deployment and dynamic runtime monitoring for malicious activity. The project uses modern DevSecOps tools to showcase a "shift-left" security approach, integrating static analysis with real-time observability.

Project Goal: Build a simulated CI/CD pipeline that:

    Scans container images (e.g., alpine:3.10) for vulnerabilities using static analysis tools like Trivy.

    Monitors running containers for performance and security issues using cAdvisor and Prometheus.
