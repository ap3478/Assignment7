Instructions:
Step 1: Install Docker
Go to Docker Links to an external site. and download Docker for your operating system: Get Started with Docker Links to an external site..

Follow the installation instructions for your operating system (Windows, Mac, or Linux).

After installing Docker, open your terminal and verify the installation by running:

docker --version
You should see the Docker version installed on your machine.

Go to DockerHub Links to an external site. and sign up for a free account. DockerHub is where you will store and share your Docker images.

Step 2: Dockerizing the QR Code Generator Application
Step 2.1: Preparing Your Project for Dockerization
Examine the provided main.py script and ensure you understand its functionality, dependencies, and configuration via environment variables.

Make sure your project has a requirements.txt file that lists all the dependencies required by the QR Code Generator. If not, generate it using:

pip freeze > requirements.txt
Initialize a Git repository if you haven't already:

git init
Add all your files and make your initial commit:

git add .
git commit -m "Initial commit of QR Code Generator application"
Push your repository to GitHub:

git remote add origin https://github.com/your-username/qr-code-generator.git
git push -u origin main
Step 2.2: Writing the Dockerfile for Your QR Code Generator
Create a new file named Dockerfile in the root directory of your QR Code Generator project.

Below is the Dockerfile tailored to your QR Code Generator application:

# Use the official Python image from DockerHub as the base image
FROM python:3.12-slim-bullseye

# Set the working directory inside the container
WORKDIR /app

# Copy the requirements.txt file and install dependencies
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Create directories for logs and QR codes, and set ownership to the non-root user
RUN useradd -m myuser && mkdir logs qr_codes && chown myuser:myuser logs qr_codes

# Copy the rest of the application’s source code into the container, setting ownership to 'myuser'
COPY --chown=myuser:myuser . .

# Switch to the non-root user for security
USER myuser

# Use ENTRYPOINT and CMD to allow flexibility when running the container
ENTRYPOINT ["python", "main.py"]
CMD ["--url", "http://github.com/kaw393939"]
Security Considerations in the Dockerfile:

Non-Root User: The Dockerfile creates a non-root user (myuser) to run the application, enhancing security by minimizing potential damage from vulnerabilities.
Ownership of Directories: Directories for logs and QR codes are created and owned by myuser, ensuring that only the non-root user can write to these directories.
Minimal Base Image: Using a slim variant of Python reduces the attack surface by minimizing the number of installed packages.
Explanation of Dockerfile Commands:

FROM: Specifies the base image (python:3.12-slim-bullseye), which is lightweight and secure.
WORKDIR: Sets the working directory inside the container to /app.
COPY: Copies requirements.txt first to leverage Docker’s caching mechanism for dependencies.
RUN: Installs Python dependencies, creates a non-root user, and sets up necessary directories with appropriate permissions.
USER: Switches to the non-root user (myuser) to run the application.
ENTRYPOINT and CMD: Defines the default command to run when the container starts, allowing for command-line argument overrides.
Step 2.3: Building and Running the Dockerized QR Code Generator
In the directory where your Dockerfile is located, run the following command to build your Docker image:

docker build -t qr-code-generator-app .
This command builds an image named qr-code-generator-app using the current directory (.) as the build context.

Once the image is built, you can run the container in detached mode:

docker run -d --name qr-generator qr-code-generator-app
This command runs the container named qr-generator based on the qr-code-generator-app image.

If your QR Code Generator application accepts custom input (such as a different URL or configuration), you can pass these to the container using environment variables or by overriding the CMD. For example:

docker run -d --name qr-generator \
-v /host/path/to/qr_codes:/app/qr_codes \
qr-code-generator-app --url http://www.njit.edu
-v /host/path/to/qr_codes:/app/qr_codes: Maps the local qr_codes directory to /app/qr_codes in the container.
--url http://www.njit.edu Links to an external site.: Overrides the default URL argument with a custom URL.
Check the container logs to ensure your QR Code Generator application is running correctly:

docker logs qr-generator
After testing, you can stop and remove the container:

docker stop qr-generator
docker rm qr-generator
Step 3: Pushing Your Docker Image to DockerHub
Once you have successfully Dockerized your QR Code Generator application, you can push the Docker image to DockerHub to share it with others or use it in cloud environments.

Run the following command to log in to your DockerHub account:

docker login
Enter your DockerHub username and password when prompted.

Tag your Docker image with your DockerHub username and image name:

docker tag qr-code-generator-app your-dockerhub-username/qr-code-generator-app