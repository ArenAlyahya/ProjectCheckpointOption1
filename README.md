# Microservices

## youtube:

https://youtu.be/CTX8tXGwPZs

## Docker Images:
1. [Jupiter Image](https://hub.docker.com/r/jupyter/scipy-notebook).
2. [Apache Hadoop Namenode](https://hub.docker.com/r/bde2020/hadoop-namenode).
3. [Apache Hadoop Datanode](https://hub.docker.com/r/bde2020/hadoop-datanode).
4. [Sonar Qube and Sonar Scanner](https://hub.docker.com/r/petronetto/sonarqube-alpine).
5. [Spark](https://hub.docker.com/r/bitnami/spark).
6. [Interface](https://hub.docker.com/r/arenalyahya/html-interface).


## Steps  to run your Docker images on Kubernetes Engine:

1. Create a project.
2. Enable GKE.
3. Enable container registry. 
4. Open Cloud shell.
5. Download Jupiter, Apache hadoop, Spark and  Sonar Qube and Sonar Scanner images and store it in container registry. 
   * Note that depending on the project ID, replace <PROJECT_ID> in the following command to the original ID.
    ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/ProjectID.png)
    
    * Jupyter:
    ```
    docker pull jupyter/scipy-notebook:latest
    docker tag jupyter/scipy-notebook gcr.io/<PROJECT_ID>/jupyter/scipy-notebook:latest
    docker push gcr.io/<PROJECT_ID>/jupyter/scipy-notebook:latest
    ```

    * Apachi hadoop namenode:
    
    ```
    docker pull bde2020/hadoop-namenode
    docker tag bde2020/hadoop-namenode gcr.io/<PROJECT>/bde2020/hadoop-namenode
    docker push gcr.io/<PROJECT_ID>/bde2020/hadoop-namenode
    ```
    * Apachi hadoop datanode:
    ```
    docker pull bde2020/hadoop-datanode
    docker tag bde2020/hadoop-namenode gcr.io/<PROJECT>/bde2020/hadoop-datanode
    docker push gcr.io/<PROJECT_ID>/bde2020/hadoop-datanode
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

6. Create a Kubernetes cluster using the following command and check the cluster existance by going to to Kubernetes Engine >> Clusters:
```
gcloud container clusters create --machine-type n1-standard-2 --num-nodes 3 --zone us-central1-a --cluster-version latest projec1kubernetescluster2 
```
![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/cluster.png)


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
         |Microsrvies| File location | Application name | port | Target port|
         |-----------|---------------|------------------|------|------------|
         |Jupiter| gcr.io/project1-327717/jupyter/scipy-notebook|jupiter|8888|8888|
         |spark|gcr.io/project1-327717/bitnami/spark|spark|8080|8080|
         |Sonar Qube and Sonar Scanner|gcr.io/project1-327717/petronetto/sonarqube-alpine|sonar|9000|9000|
        
         
      
        
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
  * This will allow us to log-in by using the password ???root???. 
 
 9. Deploy Apachi Hadoop Namenode just like step 7, but be sure to add the following environment variables, and then expose the Hadoop Namenode service using the following table:
   
   ```
  CLUSTER_NAME: test
  CORE_CONF_fs_defaultFS: hdfs://namenode:9000
  CORE_CONF_hadoop_http_staticuser_user: root
  CORE_CONF_hadoop_proxyuser_hue_groups: '*'
  CORE_CONF_hadoop_proxyuser_hue_hosts: '*'
  CORE_CONF_io_compression_codecs: org.apache.hadoop.io.compress.SnappyCodec
  HDFS_CONF_dfs_namenode_datanode_registration_ip___hostname___check: "false"
  HDFS_CONF_dfs_permissions_enabled: "false"
  HDFS_CONF_dfs_webhdfs_enabled: "true"
  ```
  ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/hadoop-namenode.png)

  

 |Microsrvies| File location | Application name | port | Target port|
 |-----------|---------------|------------------|------|------------|
 |Apachi Hadoop Namenode|gcr.io/project1-327717/bde2020/hadoop-namenode|hadoop-namenode|9870|9870|
 |Apachi Hadoop Namenode|gcr.io/project1-327717/bde2020/hadoop-namenode|hadoop-namenode|9000|9000|



 
 9. Deploy Apachi Hadoop Datanode just like step 7, but be sure to add the following environment variables:
 
 Microsrvies| File location | Application name | 
 |-----------|---------------|------------------|
 |Apachi Hadoop Namenode|gcr.io/project1-327717/bde2020/hadoop-namenode|hadoop-namenode|

   ```
  CORE_CONF_fs_defaultFS: hdfs://hadoop-namenode-service:9000
  CORE_CONF_hadoop_http_staticuser_user: root
  CORE_CONF_hadoop_proxyuser_hue_groups: '*'
  CORE_CONF_hadoop_proxyuser_hue_hosts: '*'
  CORE_CONF_io_compression_codecs: org.apache.hadoop.io.compress.SnappyCodec
  HDFS_CONF_dfs_namenode_datanode_registration_ip__hostname__check: "false"
  HDFS_CONF_dfs_permissions_enabled: "false"
  HDFS_CONF_dfs_webhdfs_enabled: "true"
  SERVICE_PRECONDITION: hadoop-namenode-service:9000
  ```


  * At the end, you will able to see all the imeges fully deployed and have services. Note: I am having difuculity with datanode.
        ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/deployment.png)

10. Test all the sevices by selecting the linkes as shown on the screenshot below. Save all the URLs for each sevice as we are going to use them in the next step:
![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/services1.png)

10.Open the intrface image folder. Open  index.html and take the the saved URLs links and hard code them in index.html program as shown below(the folloing is just an example).
   	 ```
	<a href='http://34.67.17.72:9870/dfshealth.html#tab-overview'><button>Apache Hadoop</button></a>
	
	<a href='http://35.239.49.176:8080'><button>Apache Spark</button></a>	
	
	<a href='http://34.68.157.225:8888/login'><button>Jupyter Notbook</button></a>	
	
	<a href='http://34.134.78.251:9000/about'><button>SonarQube and SonarScanner</button></a>   
	
	 ```

  * Edit the HTML button that redirects to services page. 
![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/interface3.png)

11. build the image note that you will have to change the 'arenalyahya' to usre user ID.
```
docker build -t arenalyahya/html-interface:latest .
```
12. Push it in Docker Hub
```
 docker push  arenalyahya/html-interface:latest   
```

13. download html-interface,it in container registry. Note that depending on the project ID, replace <PROJECT_ID> in the following command to the original ID.
    ```
    docker pull arenalyahya/html-interface:latest
    docker tag arenalyahya/html-interface gcr.io/<PROJECT_ID>/arenalyahya/html-interface:latest
    docker push gcr.io/<PROJECT_ID>/arenalyahya/html-interface:latest
 
    ```
14. Deploy the images to the cluster by repeating the directions in step 7 for this image by using the info in the following table:  
  
  |Microsrvies| File location | Application name |	port | Target port|
  |-----------|---------------|------------------|-------|------------|
  |Interface|gcr.io/project1-327717/arenalyahya/html-interface|htmlinterface|80|80|
         
15. Open the interface image by going to service and pressing the IP:port for the Interface serves.
![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/Interface.png)

16. Open the linkes for all the other service:
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
       ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/hadoop1.png)
       ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/hadoop2.png)

    * Spark:
       ![alt text](https://github.com/ArenAlyahya/ProjectCheckpointOption1/blob/main/screenshots/spark.png)

     

## Resources:
* https://www.dailysmarty.com/posts/steps-for-deploying-a-static-html-site-with-docker-and-nginx
* https://github.com/jupyter/docker-stacks/issues/506
* https://stackoverflow.com/questions/66063686/set-jupyter-lab-password-encrypted-with-sha-256
* https://github.com/big-data-europe/docker-hadoop/blob/master/docker-compose.yml

      




