# SQLDeveloper-M1-Setup
Step-by-step guide on installing and running SQL Developer on a Mac M1/M2 chip.  

These were the steps that helped me run SQLDeveloper with Oracle 21c (xe) on my Macbook M1, as it was part of a **Computer Science (University) course**.  
Please feel free to let me know of simplified ways, but this helped me ensure that I got to use SQLDeveloper in the same manner my (windows) classmates did, and follow along with the lecturer.  

**NOTE!** You can pull other docker images, however, for this example, I'm focusing on gvenzl/oracle-xe rather than the latest which is gvenzl/oracle-free.  


## Step One: Prerequisites
### **SQLDeveloper**: Install SQLDeveloper.app
  * Install SQLDeveloper from [Oracle Download Page](https://www.oracle.com/za/database/sqldeveloper/technologies/download/)
  * **Note**: Mac ARM64 with JDK17 included.  
  * Unzip (if not already unzipped) and drag SQLDeveloper.app into your Applications folder.  
  * 🛑 Check that JDK installed (if it was part of the download package or follow link below to install)  
    * In your terminal run:  
          `java -version`  
    You should have an output displaying the the Java version on your system. Note: It might be Openjdk, that's alright too.  
    *If Java is **NOT** installed (which was my case), you can download it from [here](https://www.oracle.com/za/java/technologies/downloads/).  
    *Rerun the above code in your terminal again, to ensure Java was installed and a version of Java is being displayed.

### **Homebrew**: Install Homebrew  
  * [Homebrew Install Page](https://brew.sh)  
  * Run the script provided on the page, it should look something like the below: (Simply copy and paste into your terminal)   
         `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` 

### **QEMU**: Install QEMU 
  * [Homebrew QEMU Page](https://formulae.brew.sh/formula/qemu)  
  * Alternatively, run the following code:  
          `brew install qemu`  

### **Colima**: Install Colima  
  * [Homebrew Colima Page](https://formulae.brew.sh/formula/colima)    
  * Alternatively, run the following code:   
        `brew install colima`   

### **Docker**: Install Docker  
  * [Homebrew Docker Page](https://formulae.brew.sh/formula/docker)  
  * Alternatively, run the following code:  
        `brew install docker`  

## Step Two: Getting Started
  ⭐ Inside your terminal, you'll run the following code.  
  * Because Oracle Database has no port for ARM Chips, a docker image needs to run through colima.  
  * Enter the following code in your terminal:  
        Run `colima start --arch x86_64 --memory 4`  
  *It will take a moment to run if you're running it the first time, but once it's done, you can move to the next step.*   
  * **You might be prompted to install QEMU, follow the instructions on your terminal to install it (If you haven't done so already).**  

### Step Two: Part Two 
 ⭐ During this step, you might be prompted to install additional packages, your terminal might display something like:  
      `run: brew install lima-additional guestagents`  
  * Run the brew command to install the additional package, once done, you should be able to get colima running successfully.  

## Step Three: Setup Oracle-xe Docker
⭐ For my course, I needed Oracle Database 21c, therefore I used the below. However, Database 23c is available on another docker image (details for latest provided on linked page).  
  * Oracle 21c [gvenzl/oracle-xe](https://hub.docker.com/r/gvenzl/oracle-xe)  
  * Pull the image you want, for example, mine was: Run this in your terminal:   
        `docker pull gvenzl/oracle-xe`  
  * Once the download is complete, you can move onto running the container.  
  * *Replace <your_password> in the below code, with your own password.*  
        Run `docker run --name oracle-xe -d -p 1521:1521 -e ORACLE_PASSWORD=<your_password> gvenzl/oracle-xe`  
      *It might take a moment.*  
  * Check that the docker is running:  
        `docker ps` 
  * If it is not running, run the below code: 
        `docker start oracle-xe` 
  * If it's running, you should receive an output of "oracle-xe" in your terminal. 

## Step Three: Check SQLDeveloper Connection 
  * Open SQLDeveloper and select ➕ in the top left corner of the screen to create **New Connection** window.  
  * Enter the name for your database, the username, password (*password you created during docker run*), and the connection should be:  
      >**Hostname**: localhost  
      >**Port**: 1521  
      >**SID**: xe  

## Additional:
### Starting and Stopping Colima and Docker  
  * Once you are done using the database it's important that you stop docker and colima, run the following in your terminal:  
       `docker stop oracle-xe` Then, once docker has stopped, stop colima:  
       `colima stop`  
  * And whenever you want to start using your databases again, run the following in your terminal:   
       `colima start` and once colima has started, start your docker:  
       `docker start oracle-xe`  

### Accessing SQLPLUS for Container DB
⭐ If you want access SQLPLUS for the container databse:  
  * Ensure your docker is running, by running the following code: **REPLACE** oracle-xe with your container name.  
        `docker start oracle-xe`  
  * Access SQLPLUS by running this code: **REPLACE** oracle-xe with your container name.  
        `docker exec -it oracle-xe bash`  
    Inside bash run: `sqlplus / as sysdba`  
  **OR**  
        `docker exec -it oracle-xe sqlplus / as sysdba`  

### Accessing Pluggable Database  
⭐ Similar to accessing SQLPlus for a container database, a pluggable database is accessed using the following code in your terminal (after starting colima and docker):   
  * **REPLACE** oracle-xe with your container name, **AND** sys123 with your oracle password:  
      `docker exec -it oracle-xe sqlplus bash`   
    Inside bash run:  `sqlplus system/sys123@localhost:1521/XEPDB1`  
  **OR**  
      `docker exec -it oracle-xe sqlplus system/sys123@localhost:1521/XEPDB1`  
  * Inside SQLDeveloper, your connection will now be:  
      >**Hostname**: localhost  
      >**Port**:  1521  
      >**Service name**:  XEPDB1  
 * It's important that you take note of the change with Pluggable databases, you no longer use SID, you use the Service name.   

  
  
  
Updated 02/07/2025  
