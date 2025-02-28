
![Areospace_Back-end(1)](https://github.com/user-attachments/assets/aa1a1d24-5c08-4b74-94f8-1dd9288e60e1)

![Static Badge](https://img.shields.io/badge/Python-3.12.3-green)
![Static Badge](https://img.shields.io/badge/InfluxDB-v1.11.8.-%20purple)


## Table of Contents
- [About](#-about)
- [How to Build](#-how-to-build)
- [Documentation](#-documentation)
- [Feedback and Contributions](#-feedback-and-contributions)
- [License](#-license)
- [Contacts](#%EF%B8%8F-contacts)

## About 🚀
This repository is designed to host the backend system to an Aerospace Corporation project. The project relays on GPS satellite tracking data to identify and analyze deadzones around the world. Its application is vast where it can be crucial in navigations, tracking and visualizing deadzones.

**Key Components**
1. **Database: InfluxDB v1**
   + **Type** : Time Series Database
   + **Purpose** : InfluxDB allows storage of time series data. In addition Influx handles high write and query loads                     typical of time series data.
   + **Benefits**:
       - **Flexibility** Influx is built to be flexible allowing for various data structures and data types.
       - **Durability** Inlfux is robust, easily handles high write and query loads which are critical to data preservation.


The project as a whole leverages Influx DB vast capabilities to provide neccessary performance to time sensitive data. It maintains the integrity of the data while also allowing the system to adapt to changing requirements.


## How to Build

To build this project 
#### Linux Guide

```shell
# Make sure python is installed, we are using python 3.12.3
# Python is installed by default on linux systems, make sure it is install 
# On (ubuntu) run this command to check

python3 --version or python --version

# If its not installed run this commands

sudo apt install python3.12

sudo apt install python3


If you are running a different version of python make sure to upgrade version by running this commands
# PPA (personal package archive are used to include software on linux systems)
# This maintainers are solid over million of downloads

sudo add-apt-repository ppa:deadsnakes/ppa

sudo apt update

sudo apt install python3.12

# Make sure Virtual Environment (VENV) is installed

sudo apt install python3-venv

```


#### Windows Guide


```shell
# Make sure python is installed, we are using python 3.12.3
# On windows download the installer from the pyhon foundation: (https://www.python.org/downloads/windows/)



```

#### Python installed on either Linux or Windows 
```shell
# If python is already installed on the system it might be best to make a Virtual Environment (VENV) since many python packages will be installed.

# If Windows use CMD, on linux use Terminal
# Navigate to desire folder for installation

# ** Linux **

python3 -m venv myenv

# Activate the environment

source myenv/bin/activate

# Deactivate VENV

deactivate

# ** Windows **

python -m venv myenv

# Activate the environment

myenv\Scripts\activate

# Deactivate VENV

deactivate

```












