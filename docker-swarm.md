# Steps to Configure Docker Swarm 

### Prerequisites: 

* Install Ubuntu Xenial on at least two VMs
* Install Docker on all VMs

### Configuration Steps

* Change Hostname for nodes

        sudo hostnamectl set-hostname <host-name>   

* Configure Hosts file on all the nodes (/etc/hosts)

        vi /etc/hosts

            <IP address of first node>  dockermanager
            <IP address of second node>  dockerworker1
            <IP address of third node>  dockerworker2

* Check connectivity between VMs

    * On `dockermanager`, run
    
            ping dockerworker1
            ping dockerworker2
            
    * On `dockerworker1` and `dockerworker2`, run
    
            ping dockerworker1

* To configure docker to run as a non-root user, run

        usermod -aG docker <username>
        
* Configure the Manager Node for Swarm Cluster Initialization

    * Initialize the Docker Swarm mode on `dockermanager`
    
            docker swarm init --advertise-addr <manager node IP address>
            
* Configure Worker Nodes to join the Swarm Cluster using the command displayed on the console of `dockermanager`

* Verify the Swarm Cluster

    * To see the node status (determine if the nodes are active/available)
    
            docker node ls
    
    * To retrieve lost join token command (run on `dockermanager`)
    
            docker swarm join-token manager
            docker swarm join-token worker

# Configure Nginx service to run on the swarm 

* Deploy Nginx service on Swarm Cluster

        docker service create --name <service-name> --publish <exposed-port>:<internal port> --replicas <number-of-replicas> <service-image-name>
        
        docker service create --name myNginxServer --publish 8081:80 --replicas 2 nginx
        
* To list services deployed on the swarm

        docker service ls
        docker service ps <service-name> 
        
        docker service ps myNginxServer
        
* To check if the service is running

        curl http://dockermanager:<exposed-port>
        
        curl http://dockermanager:8081
        
* To scale the service 
        
        docker service scale <service-name>=<number-of-replicas>
        
        docker service scale myNginxServer=5
        
* To check the output after scaling

        docker service ls
        docker service ps <service-name> 
        
        docker service ps myNginxServer
        
* To see extended details of the deployed service

        docker service inspect
