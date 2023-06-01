# Use an official Python runtime as a parent image
FROM python:3.8-slim-buster

# Set the working directory in the container
WORKDIR /usr/src/app

# Install Jupyter and other required libraries
RUN pip install --no-cache-dir jupyter numpy pandas scikit-learn

# Make port 8888 available to the world outside this container
EXPOSE 8888

# Run jupyter notebook when the container launches, and also add a no-password option
CMD ["jupyter", "notebook", "--ip='*'", "--port=8888", "--no-browser", "--allow-root", "--NotebookApp.token=''", "--NotebookApp.password=''"] 
