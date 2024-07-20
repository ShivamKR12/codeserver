Setting Up Jupyter Lab on Google Colab

This guide will walk you through the steps to set up Jupyter Lab with necessary extensions, GPU support, and expose it using ngrok on Google Colab.

Requirements

Google Colab environment
ngrok account with an authentication token

Steps

Step 1: Install Jupyter Lab and Required Packages

First, install Jupyter Lab and other required packages:
`!pip install jupyterlab jupyterlab-code-formatter jupyterlab-lsp python-lsp-server[all]`

Step 2: Install ngrok to Expose the Jupyter Lab Server

!pip install pyngrok

Step 3: Install Jupyter Lab Extensions

Install the language server and code formatter extensions:

!jupyter labextension install @krassowski/jupyterlab-lsp
!jupyter labextension install @ryantam626/jupyterlab_code_formatter

Step 4: Enable Jupyter Lab Extensions

Enable the code formatter server extension:

!jupyter serverextension enable --py jupyterlab_code_formatter

Step 5: Install GPU Packages (TensorFlow, PyTorch)

!pip install tensorflow-gpu
!pip install torch torchvision torchaudio

Step 6: Generate Jupyter Lab Configuration

Generate the default Jupyter Lab configuration file:

!jupyter lab --generate-config

Step 7: Configure Jupyter Lab

Append the following configuration to your jupyter_lab_config.py:

config_path = "/root/.jupyter/jupyter_lab_config.py"
with open(config_path, "a") as config_file:
    config_file.write("""
# Add the following lines to your jupyter_lab_config.py

c.ServerApp.port = 8888
c.ServerApp.open_browser = False
c.ServerApp.ip = '0.0.0.0'
c.ServerApp.allow_remote_access = True
c.ServerApp.token = ''  # No token authentication
c.ServerApp.password = ''  # No password authentication
c.LspInstallSettings.install_optional_dependencies = True

# Configure code formatter
c.CodeFormatter.formatters = {
    'python': ['black'],
}

# Enable GPU support
c.NotebookApp.kernel_spec_manager_class = 'nb_conda_kernels.CondaKernelSpecManager'

# Save notebook state
c.NotebookApp.shutdown_no_activity_timeout = 3600
c.NotebookApp.terminals_enabled = True
""")

Step 8: Start ngrok

Authenticate ngrok with your authtoken and set up a tunnel for the Jupyter Lab server:

from pyngrok import ngrok

# Authenticate ngrok with your authtoken
ngrok.set_auth_token("YOUR_NGROK_AUTH_TOKEN")

# Kill any previous ngrok sessions if running
ngrok.kill()

# Set up a tunnel for the Jupyter Lab server
public_url = ngrok.connect(8888)
print(f"Jupyter Lab public URL: {public_url}")

Step 9: Start Jupyter Lab Server

Finally, start the Jupyter Lab server:

!jupyter lab --ip=0.0.0.0 --port=8888 --no-browser --allow-root
