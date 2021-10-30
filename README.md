# Microservices

## Docker Images:
1. [Jupiter Image](https://hub.docker.com/r/jupyter/scipy-notebook).
2. [Apache Hadoop](https://hub.docker.com/r/harisekhon/hadoop).
3. [Sonar Qube and Sonar Scanner](https://hub.docker.com/r/petronetto/sonarqube-alpine).
4. [Spark](https://hub.docker.com/r/bitnami/spark).
5. [Interface](https://hub.docker.com/r/arenalyahya/html-interface).


## Steps  to run your Docker images on Kubernetes Engine:

1. create a project.
2. Enable GKE
3. Enable container registry. 
4. Open Cloud shell
5. download Jupiter, Apache hadoop, Spark and  Sonar Qube and Sonar Scanner images and store it in container registry. Note that depending on the project ID, replace <PROJECT_ID> in the following command to the original ID.
    * Jupyter:
    ```
    docker pull jupyter/scipy-notebook:latest
    docker tag jupyter/scipy-notebook gcr.io/<PROJECT_ID>/jupyter/scipy-notebook:latest
    docker push gcr.io/<PROJECT_ID>/jupyter/scipy-notebook:latest
    ```

    * Apachi hadoop:
    ```
    docker pull harisekhon/hadoop:latest
    docker tag harisekhon/hadoop gcr.io/<PROJECT>harisekhon/hadoop:latest
    docker push gcr.io/<PROJECT_ID>/harisekhon/hadoop:latest
    ```

    * Sonar Qube and Sonar Scanner:
    ```
    docker pull petronetto/sonarqube-alpine:latest
    docker tag petronetto/sonarqube-alpine gcr.io/<PROJECT>/petronetto/sonarqube-alpine:latest
    docker push gcr.io/<PROJECT_ID>/petronetto/sonarqube-alpine:latest
    ```

    * Spark:
    ```
    docker pull bitnami/spark:latest
    docker tag bitnami/spark gcr.io/<PROJECT>/bitnami/spark:latest
    docker push gcr.io/<PROJECT_ID>/bitnami/spark:latest
    ```

6. Create a Kubernetes cluster:
```
gcloud container clusters create --machine-type n1-standard-2 --num-nodes 3 --zone us-central1-a --cluster-version latest projectkubernetescluster
```

7. Deploy the images to the cluster:
    * Jupyter: 
      * Open the Container Registry
      * Choose gcr.io/project1-327717/jupyter/scipy-notebook
      * Choose (deploy to GKE)
      ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/1.png)
      * Press continue:
      ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/2.png)
      * Change the application name to jupyter and press deploy:
      ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/3.png)
      * Press expose to create the service: 
      ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/4.png)
      * Fill the the port field with 8888 and target port with 8888 and make sure that the option is load balencer. Then select expose:
       ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/5.png)
       * repeat the directions in step 7 for all the other images where the valuse 
         |Microsrvies| File location | Application name |	port | Target port|
         |-----------|---------------|------------------|------|------------|
         |Jupiter| gcr.io/project1-327717/jupyter/scipy-notebook|jupiter|8888|8888|
         |Apachi Hadoop|gcr.io/project1-327717/harisekhon/hadoop|hadoop|50070|50070|
         |spark|gcr.io/project1-327717/bitnami/spark|spark|8080|8080|
         |Sonar Qube and Sonar Scanner|gcr.io/project1-327717/petronetto/sonarqube-alpine|sonar|9000|9000
         
        * At the end, you will able to see all the imeges fully deployed and have services.
        ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/6.png)

        
8. Enable using passwords in Jupyter rather than tokens to overcome the problem of generated random tokens. We have to make a constant password that will allow use it all the time. 
  * Go to workloads in Kubernetes Engine.
  * Choose jupyter.
  * Choose YAML.
     ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/7.png)

  * Edit the YAML file by adding the following two lines after the image label.
  ```
  image: gcr.io/project1-327717/jupyter/scipy-notebook:latest
  command: ["start-notebook.sh"]
  args: ["--NotebookApp.password='sha1:4e9c0a22901f:d54fb752d93d1ca5d2916d46f8758f43587eecaf'"]
  ```
  * This will allow us to log in by using the password ‘root’. 
9. Test all the servieses:
10. Take the URL from every webpage URL links and hard code the index.html program.
![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/HtmlCode.png)
11. build the image
```
docker build -t arenalyahya/html-interface:latest .
```
12. push it in Docker Hub
```
 docker push  arenalyahya/html-interface:latest   
```

13. download Jupiter, Apache hadoop, Spark and  Sonar Qube and Sonar Scanner images and store it in container registry. Note that depending on the project ID, replace <PROJECT_ID> in the following command to the original ID.
    ```
    docker pull arenalyahya/html-interface:latest
    docker tag jupyter/scipy-notebook gcr.io/<PROJECT_ID>/arenalyahya/html-interface:latest
    docker push gcr.io/<PROJECT_ID>/arenalyahya/html-interface:latest
   
    ```
14. Deploy the images to the cluster by repeating the directions in step 7 for this image by using the info in the following table:  
  
  |Microsrvies| File location | Application name |	port | Target port|
  |-----------|---------------|------------------|-------|------------|
  |Interface|gcr.io/project1-327717/arenalyahya/html-interface|htmlinterface|80|80|
         
15. Open the interface image by going to serveis and pressing the IP:port for the Interface serves.
![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/Interface.png)

16. Open the linkes for all the other servese:
    * Jupyter:
    
      * Enter the password 'root'
            ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/jupyter1.png)
            ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/jupyter2.png)

    * Sonar Qube and Sonar Scanner:
      * Enter the user 'admin' and the password 'admin' 
        ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/sonar1.png)
        ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/sonar.png)
        ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/sonar3.png)

    * Apachi hadoop:
       ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/hadoop.png)

    * Spark:
       ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/spark.png)

     


      




