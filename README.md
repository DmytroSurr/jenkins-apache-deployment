# Jenkins CI/CD Pipeline for Apache Deployment

## 1. Objective

The objective of this assignment is to install Jenkins, create a CI/CD pipeline, deploy the Apache HTTP Server on Ubuntu Server 24.04, and verify the deployment. The assignment also includes generating HTTP errors and analyzing Apache log files.

## 2. Environment

- **Operating system:** Ubuntu Server 24.04 LTS
- **CI/CD tool:** Jenkins
- **Pipeline language:** Groovy (Declarative Pipeline)
- **Version control:** Git and GitHub
- **Web server:** Apache HTTP Server
- **Testing tool:** curl

## 3. Pipeline Configuration

The Jenkins pipeline is stored in a `Jenkinsfile` in a public GitHub repository:

https://github.com/DmytroSurr/jenkins-apache-deployment

Jenkins retrieves the pipeline definition from the `main` branch using **Pipeline script from SCM**.

The pipeline consists of four stages:

### 3.1 Check Environment

Checks the Ubuntu environment and verifies that Java and Git are available.

### 3.2 Deploy Apache

Executes a dedicated deployment script that updates package information, installs Apache and curl, creates a test web page, enables the Apache service, and verifies that the service starts successfully.

### 3.3 Verify Apache Service

Checks whether the Apache service is active and displays the installed Apache version.

### 3.4 HTTP Smoke Test

Uses curl to send an HTTP request to the local web server. The test retries the request up to five times if the service is not immediately available. The stage fails if the HTTP check does not succeed.

## 4. Improvements to the Jenkinsfile

The improved pipeline includes several useful practices:

- **Declarative Pipeline structure:** separates deployment into clear, maintainable stages.
- **Repeatable deployment:** the deployment script installs missing packages and configures the web server.
- **Service verification:** checks that Apache is running after deployment.
- **HTTP health check:** verifies that the web server responds successfully.
- **Retry logic:** allows a short recovery period before declaring the HTTP check unsuccessful.
- **Build feedback:** the `post` section reports success or failure.
- **Execution timestamps:** helps identify when individual pipeline steps run.
- **Concurrent build prevention:** avoids simultaneous executions of the same job.
- **Restricted administrative privileges:** Jenkins can execute a specific root-owned deployment script through a limited sudoers rule rather than running the entire Jenkins service as root.

## 5. HTTP Error Log Analysis

To test Apache access logging, requests were generated for different HTTP response codes.

### HTTP 404 — Not Found

A request was sent to a nonexistent page:

`/homework-page-does-not-exist`

Apache returned HTTP 404. The request was confirmed in `/var/log/apache2/access.log`.

### HTTP 500 — Internal Server Error

A dedicated CGI test script was configured to return HTTP 500 intentionally.

The request to `/cgi-bin/homework-error.sh` returned HTTP 500, which was also recorded in the Apache access log.

The logs additionally contained other HTTP status codes, including 408. These entries demonstrate how Apache records unsuccessful and incomplete requests.

## 6. Results

The Jenkins pipeline completed successfully.

The following results were verified:

- Jenkins retrieved the pipeline from GitHub.
- Apache was installed and started successfully.
- The test web page was accessible over HTTP.
- The HTTP smoke test passed.
- HTTP 404 and HTTP 500 responses were generated and identified in the Apache access log.

## 7. Conclusion

This assignment demonstrates how Jenkins, GitHub, and Apache can be combined to implement a basic CI/CD workflow. The pipeline automates web server installation and configuration, checks service availability, and validates the HTTP response.

The improved solution is easier to maintain and troubleshoot because it separates deployment, service verification, and HTTP testing into distinct stages. Restricting administrative privileges also reduces the permissions granted to the Jenkins service.
