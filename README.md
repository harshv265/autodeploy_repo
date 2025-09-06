# Autodeployment System via Natural Language

This project provides a system, implemented in a Colab/Jupyter Notebook, that automates the deployment of web applications to AWS. It accepts natural language instructions and a source code repository to provision infrastructure and deploy the application with minimal user intervention.


##  Features

-   **Natural Language Instructions**: Specify deployment needs (e.g., "Deploy this Flask app on AWS in us-east-1") in plain English.
-   **Code Analysis**: Intelligently inspects a GitHub repository or uploaded ZIP file to detect the framework (Node.js, Flask, Django, FastAPI), dependencies, and required start commands.
-   **Infrastructure as Code (IaC)**: Automatically generates a Terraform plan to create the necessary AWS infrastructure. For this demo, it provisions a single EC2 instance.
-   **Automated Provisioning & Deployment**: Executes the Terraform plan to build the infrastructure, installs the required runtime (Node.js/Python), clones the repository, and starts the application using `systemd`.
-   **Dynamic Configuration**: Performs a best-effort replacement of `localhost` with the instance's public IP address in common configuration files.
-   **Lifecycle Management**: Provides simple one-click buttons to **Analyze**, **Plan**, **Deploy**, and **Destroy** the entire stack.

##  Prerequisites

Before running the notebook, ensure you have the following:

1.  **Python 3**: The notebook is written in Python.
2.  **Terraform**: Version `>= 1.5` must be installed and available in your system's PATH. The setup cell in the notebook includes a script to install it on Colab environments.
3.  **AWS Account**: You need an AWS account with credentials (**AWS Access Key ID** and **Secret Access Key**) that have permissions to create EC2 instances and related resources (like Security Groups).

##  How to Use

1.  **Setup**: Open `Autodeploy.ipynb` in Google Colab or a local Jupyter environment and run the **"0) Setup & Dependencies"** cell to install required Python packages and ensure Terraform is present.
2.  **Inputs**: Run the **"1) Inputs — Natural Language + Repo"** cell to render the user interface.
3.  **Configure Deployment**:
    * **Instruction**: Enter a natural language command (e.g., `Deploy this Node app on AWS in us-west-2. Open port 8080.`).
    * **GitHub URL / ZIP Upload**: Provide the URL to your application's GitHub repository or upload it as a `.zip` file. A sample repo is pre-filled: `https://github.com/Arvo-AI/hello_world`.
    * **AWS Credentials**: Enter your AWS Key ID and Secret.
    * **Dry Run (Optional)**: If you want to see the generated Terraform plan without creating any resources, check the **Dry Run** box.
4.  **Analyze**: Click the **Analyze Repo** button. The system will clone the repository and print its analysis of the framework, port, and start command.
5.  **Generate Plan**: Click the **Generate Plan** button. This creates a deployment plan based on your instructions and the code analysis.
6.  **Provision & Deploy**: Click the **Provision & Deploy** button. The notebook will run `terraform init`, `plan`, and `apply` to create the resources on AWS. The application URL will be displayed in the output once finished.
7.  **Clean Up**: After you are done, click the **Destroy Infra** button to tear down all created AWS resources and avoid incurring further costs.

##  Technical Details

-   **Provisioning**: The backend uses **Terraform** to provision a single Ubuntu EC2 instance on AWS.
-   **Deployment Strategy**: A `user_data` script is passed to the EC2 instance. This script is responsible for:
    -   Installing runtimes (Node.js via Nodesource, Python via `apt`).
    -   Cloning the application repository from the provided URL.
    -   Installing application dependencies (`npm install` or `pip install`).
    -   Creating and enabling a `systemd` service to run the application and ensure it restarts on failure.
-   **Future Work**: The codebase includes placeholder hooks to extend the deployment strategies to other platforms like AWS Lambda (serverless) or Kubernetes (ECS/EKS).
