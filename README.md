# market-place-kubernetes
This project has all the Kubernetes' configuration files to all external docker images the Market Place system uses. Such as MongoDB, RabbitMQ and anything else needed.

### Introduction
Hi! First of all, thank you very much for stopping by and know that this system is licensed under the "DO WHATEVER THE =D YOU WANNA DO" License. 
I Vanderson Assis (assis.vanderson@gmail.com) created this system as a portifolio to show any interviewers my experience in a microservices environment. Also have in mind I'm always 
improving my knowledge by <b>studying literally every day</b>. So at this point in time, I created this system believing this was the best approach to do it, maybe by the time you're reading this I 
already think differently and would do it in another way. If you have any problem on deploying it, please get in touch through my e-mail and I'm going to be more than pleased in helping you.

### Contact:
  name: Vanderson Assis
  e-mail: assis.vanderson@gmail.com
  linkedin: https://www.linkedin.com/in/vanderson-assis-a2541257/

### Overview:
So after running the Deploy commands having the Tools needed bellow, you'll be able to consume the endpoints provided by this amazing, resilient system. =] ...
Please visit the following repository to know how to consume the endpoints https://github.com/VandersonAssis/market-place-products.
A little bit of context:
<b>MongoDB:</b> is set up in a Replica Set Kubernete's Statefulset with three Replicas, meaning that, it's resilient and fault tolerant. So if any of the replicas 
		 goes down or even ALL of them, the system will recover and no data will be lost. At most a hiccup on the endpoint calls IF and only IF by a miracle, 
		 all the replicas falls at the same time.

<b>RabbitMQ:</b> Rabbitmq is set to have two replicas, meaning if one goes down, the system will keep functioning. Even if for a really bad luck all the replicas goes down 
		  the system will be able to recover since they're configured in a Kubernetes Statefulset and the rabbitmq data is being saved in a separated volume.

<b>Zipkin:</b> Zipkin is set in a Deployment, also meaning that if it goes down, it'll get back up automatically. It's using Kafka to send its data to a mysql database. 
		So, again, no data loss.

<b>Microservices (products, sellers, purchase, orderprocessor):</b> They're also configured in a Statefulset and having their database in the MongoDB Replica Set mentioned above.
															 Meaning, if any of them goes down, the system will freaking recover by itself and no data loss. Amazing, isn't? =]

<b>Frontend:</b> This system will have its frontend created with React, which will also be in Kubernetes with Replicas and so on. RESILIENCE MY FRIEND!

Bellow you'll see the tools and versions needed to deploy this system. Of course, if you're using newer or older versions of them, give it a shot; it'll probably work.
### Tools:
Kubectl [1.18]
Minikube [version v1.9.0]
Docker [version 19.03.8]
Linux Ubuntu [18.04] (This deploy will probably work in any OS, but for the sake of information, this is the OS used on the development of it)

### Deploy:
Your Minikube should have at least 6GB of memory available to use, less than this can leave the system unstable. Just to make sure, run `minikube delete` WARNING! After starting your Minikube 
again, the pods will <b>not</b> start automatically! So before running it, make sure you have all the yaml files for your pods and so on.
After executing the delete command then run `minikube start --memory 6144`. By doing this, your Minikube will use up to 6GB of memory. It's important to note that you have to 
<b>delete</b> your minikube in order for it to start again with 6GB available.

Then run `minikube tunnel` command, so minikube can emulate LoadBalancer services which are used in this system's microservices.

If you have all the above mentioned technologies installed, all you'll have to do is; clone this repository to your computer, navigate to it's location, then access the "system" folder using a command line with access 
to Kubectl commands and execute `kubectl create -f market-place.yaml`. Depending on the power of your machine, the system will be deployed in less than a minute or some minutes.
Unfortunately by this time I couldn't make the rabbitmq PersistentVolume bind to the NFS server by it's service name, it's using IP instead. So all you have to do is:
- The NFS have already been deployed on the command above mentioned. So please execute `kubectl describe pod nfs-server-pod | grep IP:`
- Copy the IP address, in my case it is "172.17.0.17"
- Paste the above copied IP address into the "market-place-rabbitmq.yaml" file. The property in this file that you'll have to paste the IP is the nfs server property located in 
the "rabbitmq-data" PersistentVolume. Remember, you can always count on our "ctrl f" friend to locate the right spot on the "market-place-rabbitmq.yaml" file.
- After pasting the IP address CORRECTLY, then run `kubectl create -f market-place-rabbitmq.yaml`. Now run the command `kubectl get pods` to list the pods and when you 
 have two instances of rabbitmq running (something like: rabbitmq-0 1/1 running and rabbitmq-1 1/1 running), then you're good to go. The system is deployed!
  
### Throubleshoot:
One thing I've noticed is that in some machines, the zipkin pod doesn't connect correctly to the zipkin-mysql pod, which is the one that persists zipkin's data. If that happens to you, 
please follow bellow steps:
- Run `kubectl exec -it zipkin-mysql-db96f6f4-cqhk2 -- sh`. Replace the pod's name (zipkin-mysql-db96f6f4-cqhk2) to yours. To get the pod name in your machine, run `kubectl get pods` 
  copy and paste your zipkin-mysql pod name into the previously mentioned command
- Then run `mysql -u zipkin -p` and on the password, type zipkin
- Then run `show databases;`
- After that exit the pod by running `exit` command twice.
- Run kubectl get pods again in around 10 secs. If zipkin pod still didn't start correctly then
- Delete the zipkin pod by running `kubectl delete pods zipkin-7f4d9b5487-qbwsz`. Again, replacing the pod's name by yours.
- After you delete the pod, the deploy will take care of starting another one and this time it will start correctly. If it doesn't, reach out to me! =]

Also, for NOW some of the ports had to be configured as statics, which can clash with the ports you're probably already using on your stuff. A next version of this system will have totally dynamic ports. 
I believe that changing it right now to dynamic, will not break anything, but since I don't have the time now to make sure of it, please keep it as it is.

#### Disclaimer
This and any other piece of code belonging to the Market Place project is 
being created with intention to show interviewers my abilities creating 
a system using microservices architecture.

Also, please have in mind that 
I work on this project only on my free time, therefore, improvements can take a little while to be made.

Last but not least, feel free to do whatever the h311 you want with this code. :D