# INTRODUCTION TO LINUX
Mastering Linux is fundamental essential as a DevOps engineer. This project equip me with a solid foundation in Linux.

## What is Linux?
Linux is a free, open-source operating system similar to Windows or MacOS, but it's more widely used for servers and supercomputers. It's known for its stability, security, and flexibility, allowing users to modify and distribute their version.

## Linux Distributions
Some of the Linux distribution are;
1. Ubuntu
2. CentOS
3. Debian
4. Fedora
5. RedHat

## Installtion and initial Setup
In this section I will be demostarting all the steps I took in creating a server (VM) in the cloud (AWS), and also how I gain access to it from my local enviroment (My laptop).
AWS, is a cloud provider to create the server in the cloud. AWS provide us with a free vitual server called EC2 ` **Elastic Compute Cloud** `.
I was able to create the server on AWS successfully by watching the 2 video provider by the instructor 

    1. [AWS account setup and Provisioning an Ubuntu Server](https://www.youtube.com/watch?v=xxKuB9kJoYM)

    2. [Connecting to your EC2 Instance](https://www.youtube.com/watch?v=TxT6PNJts-s)

and also the guildline provider in the course.

These are the steps involves:
1. I open my browser then go to AWS official website
![1. AWS Website](1.%20AWS%20Website.png)
2. I sign in into my AWS account because I already create an account with AWS in our previous project, by clicking on `Sign In to the Console`, then login as `Root user`, then I input my email address and my password.
![2. Sign in as a Root User](2.%20Signin%20as%20Root.png)
3. Below is my interface on AWS after I login,
![3. After Login to AWS](3.%20After%20Login%20to%20AWS.png)
Then I search for Elastic Cloud Compute (EC2) at the top left side 
![4. Search for EC2](4.%20Serach%20for%20EC2.png)
4. Then from the menu on the left side, I select `Instances`, then `Launch instances`.
![5. Launch instances](5.%20Launch%20instances.png)
5. Then I configure the instances by providing a name which is `Ubuntu_Server`, selecting Ubuntu as the OS image to use, creaing a key pair which I name `Ubuntu_Server_Key` the storage to be `20GB`.
![6. Configure Instances](6.%20Configure%20Instances.png)
6. Finally I click on `Launch Instances` and it was successful and running.
![7. Successfully Created](7.%20Ubuntu%20Created.png)
![8. Ubuntu Running](8.%20Ubuntu%20Running.png)


## Connecting to the Server
These are the steps I took to connect to my server on the cloud (AWS):

the client tools I used is `GitBash` which I have already installed on my laptop, the reason why I make use of GitBash is to allow me to use linux command like `chmod` `cd` and the likes.

1. I open my GitBash application on my laptop, then move to my `Download` folder using `cd ~/Downloads` and I was able to location the key I download from the AWS.
![9. GitBash](9,%20GitBash.png)
2. After that I ran `chmod 400 Ubuntu_Server_Key.pem` because Ubuntu_Server_Key.pem is the name of my key.
3. Then I went back to my new server that is running presently, to get my public ip address which happen to be `43.204.214.121`.
![10. Ip Address](10.%20Ip%20Address.png)
4. After that I went back to my GitBash application to continue my connection between my local laptop to the cloud environment. Then I ran `ssh -i Ubuntu_Server_Key.pem ubuntu@43.204.214.121` which connect me to my server on the cloud (AWS).
![11. GitBash Connection](11.%20Git%20Connection.png)

## Package Managers
The package manager I will be using is `apt-get` because what I install is under Debian family distribution `Ubuntu`

## Installing, Updating and Removing Software

1. The first thing I did was to update all my existing packages, the command I use is `sudo apt-get update`
![12. Update](12.%20Update.png)
2.  Installing Software Packages
I try to install `tree`, which is a command commonly used to visually see file system structure on a linux servers, so I installed the package with this command `sudo apt-get install tree`.
![14. Install Tree](14.%20Install%20Tree.png)
3. Verifying Installed Packages: then I ran `tree -L 1 /` to check if the package is install properly.
Note the L 1 means I only want the see level 1 because if I don't indicate that `tree` command will try to check all the directory one after the other which will take a lot of time.
![15. Confirm Tree](15.%20Comfirm%20tree.png)
4. Updating Installed Packages: After I finshed updating all the packages I also make sure to upgrade them to the latest version by running this command, `sudo apt-get upgrade -y`.
![13. Upgrade](13.%20Upgrading.png)
5. Removing Software Package: I try to remove `tree` package I installed earlier, with this command `sudo apt-get remove tree`
![16. Remove Tree](16.%20Remove%20Tree.png)
