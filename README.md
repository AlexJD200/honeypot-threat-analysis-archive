# Honeypot Threat Analysis

## Archive Disclaimer

The project in this repository is an archive of the work submitted for my BSc Computer Science dissertation. To keep the repository lightweight, 
it omits the large database file needed to re-run the analysis locally, but the code and saved results can still be viewed in the `notebooks/` folder.

I have also included the 10-page project report in `report/`, which walks through the full process and findings - I recommend starting there if you want a quick overview of the project. 

## Project Overview
This project analyses 4 weeks of attack data collected from a distributed medium-interaction honeypot network (Cowrie) deployed on AWS. The analysis focuses on attacker geolocation, common credential permutations, and command injection patterns. Please see the project report for further information.

## GitHub Repository Links
The below links are to the three public GitHub repositories that were created to emulate the accidental "leaking" of keys. See section 4.2.4 of the report for further information.

https://github.com/deploybackup2025/dotfiles-local

https://github.com/deploybackup2025/server-automation-scripts

https://github.com/deploybackup2025/legacy-db-migration

## Repository Structure
* `notebooks/`: Jupyter Notebooks containing the data analysis code and results.
* `data/`: Contains the SQL dump of the captured attack data. *(Not included in archive - See disclaimer)*
* `report/`: Contains a copy of the project report. Contains a log of Semester 1 work in `report/Semester_1_Work`.
* `credentials/`: Contains copies of the `userdb.txt` files for Honeypots 1 and 2. (See 4.2 of the report)
* `docker-compose.yml`: Creates the analysis environment.
* `requirements.txt`: Python dependencies.

## Getting Started

There are two ways to view the analysis notebooks in `notebooks/`:

1. If you only want to **view** the code and analysis (without running the code): 
You can open the notebooks without any further setup. The code and results (including all graphs)
have been saved within the notebooks. You can skip `01_Connection_Test.ipynb` as that
is just a test notebook used if you are following the below guide. <br>


2. If you want to **run** the code: You need to have a copy of the database setup
locally. To do this, follow the below setup guide.

## Setup Guide for Running the Code (For Reference Only - See Disclaimer)

You only need to follow the below guide if you want to **run** the code. If you only want to view
the code and results, you **do not** need to follow this guide (see section above). 

To run the code locally, you do not need to install a database server manually. The project uses Docker to create the MariaDB environment in a container.

### Prerequisites
* **Docker Desktop**
* **Python 3.14**
* **Git**
* ``git-lfs`` (If not already included in your git installation)

### 1. Environment Setup
The project uses Docker Compose to create a local MariaDB instance and automatically import the data.

1.  Clone the repository and open your terminal in the project root.

* *Note: The project uses **Git LFS** for the database dump. Ensure the `data` folder contains the full ~200MB file `cowrie_final.sql.gz`. If you see a small 1KB pointer file instead, run ``git lfs install`` and then ``git lfs pull`` inside the project root directory*

2.  Run the following command:
    ```bash
    docker compose up -d
    ```

3. Wait for Data Import:
   
    Docker will automatically extract and import the SQL dump. The initial command completes quickly, but the background import **may take 5-10 minutes**.    
    * To monitor progress, run: ``docker logs -f honeypot_db``
    * The database is ready when you see: ``mariadbd: ready for connections``

*Once this is complete, the database will now be listening on `localhost:3306` (User: `root`, Password: `root`).*

### 2. Python Setup
I recommend using a python virtual environment when running the project. To do this, run the following commands inside the root directory of the project:

```bash
python3 -m venv venv

#Windows
.\venv\Scripts\activate

#MacOS/Linux
source venv/bin/activate
```

Then install dependencies:
```bash
pip install -r requirements.txt
```

### 3. Running the Analysis
1. Start Jupyter Lab (or open the project in PyCharm/VS Code)
    ```bash
    jupyter lab
    ```
2. Open `notebooks/01_Setup_Connection.ipynb` and run the first code block to verify the database connection works. You should see a success message with 5 attack logs listed. 

**If following this guide it is also important to run the second code block. (View the notebook for further info)**

3. Proceed through the other analysis notebooks in any order you please (although I recommend viewing them in order). When running code, **use the "run all"** button rather than running the blocks individually as code in later blocks often relies on code from earlier blocks to work.

## Pausing and Restarting the Container
To pause the Docker container without deleting it, use the following command:
```bash
docker stop honeypot_db
```
To start the container up again:
```bash
docker start honeypot_db
```
*NOTE: On system restart the container will turn off and will need to be started again*

## Deleting the Container
When finished, you can delete the container with the following command:
```bash
#Run in project root directory
docker compose down
```

