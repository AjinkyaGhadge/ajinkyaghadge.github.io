---
title: "Dockerizing a web application for deployment to Heroku"
excerpt: "Creating a simple API and containerzing it"
header:
  teaser: assets/images/flaskmlapp.png
---
# Architecture

![architecture](/assets/images/flaskmlapp.png)


Note: This implementation was tested on Ubuntu 20.04, which is the recommended environment.

# Steps to build and test
1. Run the command ```sudo docker build -t flask-mlapplication:latest .``` and wait for the build process to complete
2. Run the command ```sudo docker run --name flask-mlapplication -p 5000:5000 flask-mlapplication```, this will start the server.
3. Open the testfolder folder, and paste the images you want to test
4. Open another terminal Run the python test script using the command ```python testscript.py```
