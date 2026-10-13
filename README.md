# Docker Linux Machine Hosting Web Application

This project launches an Amazon EC2 instance, installs Docker, builds a custom Nginx image from a Dockerfile and HTML file, runs it as a container, and serves the web page over HTTP from the instance's public IP.

**Skill:** Hosting Web application from Docker Image
**Course:** IT 442 Cloud Computing, George Mason University (Lab 8: Installing Docker and Docker EC2 Image)

## Architecture

Browser → EC2 instance (`t2.micro`, Amazon Linux 2023) on port 80 → Docker container (`my-container`, renamed `madeleine`) → Custom image `http-app` built from `nginx:latest` with `index.html`

## Steps

### 1. Launch the EC2 instance
- Name: `Docker EC2 instance`
- AMI: Amazon Linux 2023
- Instance type: `t2.micro`
- Key pair: created for SSH access
- VPC: default VPC
- Auto-assign public IP: Enable
- Security group: `launch-wizard-1`
  - Rule 1: SSH (TCP 22) from anywhere
  - Rule 2: HTTP (TCP 80) from anywhere

### 2. Connect to the instance and create the image files
On the instance, I created two files:

`dockerfile`
```dockerfile
# Use the official Nginx image as a base
FROM nginx:latest

# Copy the HTML file into the Nginx web directory
COPY index.html /usr/share/nginx/html/

# Expose port 80
EXPOSE 80
```

`index.html` contains the lab completion page.

### 3. Build the Docker image
```bash
docker build -t http-app .
docker images
```
The build completed successfully, creating the image `http-app:latest`.

### 4. Run the container
```bash
docker run --name my-container -d -p 80:80 http-app
docker ps
docker rename my-container madeleine
```
The container started with port 80 mapped to the host.

### 5. Verify the web server
- Opened `http://<EC2_PUBLIC_IP>` in a browser.
- The page loaded from the Docker container and showed the lab completion message.

### 6. Stop the container
```bash
docker stop <container_id>
docker ps
```
`docker ps` showed no running containers afterward.

## Verification

| Check | Result |
|---|---|
| EC2 instance status | Running, 2/2 checks passed |
| Docker image build | `http-app:latest` created from the Dockerfile |
| Container run | Running on port 80, renamed to `madeleine` |
| Web page | Served through the container at the instance's public IP |
| Container stop | Stopped successfully, `docker ps` empty |

## Notes

- The security group allows SSH and HTTP from anywhere (`0.0.0.0/0`). This is acceptable for a lab but should be restricted in production.
- Instance IDs, IP addresses, DNS names, and account numbers have been redacted.

## Cleanup

Terminate the EC2 instance after grading to avoid charges.
