
# Build and Install open62541 (OPC UA in C) Server on Linux! 


**open62541** is an open-source implementation of OPC UA (Open Platform Communications Unified Architecture) protocol stack. OPC UA is a machine-to-machine communication protocol for industrial automation developed by the OPC Foundation. It provides a standardized, platform-independent communication for interacting with industrial equipment and systems

## Manual Installation
In this step by step manual installation guide, we will install open62541 server on linux machine (ubuntu 22.04.4 LTS). 

Download **open62541** from [here](https://github.com/open62541/open62541) 

**open62541** can be installed using the well known **cmake** tool making use of its **make install** command. This allows you to use pre-built libraries and headers for your own project.

To override the default installation directory use **cmake -DCMAKE_INSTALL_PREFIX=/some/path**. Based on the SDK Features you selected, as described in Build Options, these features will also be included in the installation. 

First of all open Linux Terminal and then select the directory where you want to install **open62541**.

Next, clone the open62541 repository using the following command

```bash
sudo git clone https://github.com/open62541/open62541.git
```
After cloning the open62541 repository, get into open62541 directory.  
```bash
cd open62541
```
Next, we have to initialize and update Git submodules within the downloaded  repository by using the following command. 
```bash
sudo git submodule update --init --recursive
```
Now create the **build** directory inside the open62541 directory 
```bash
sudo mkdir build && cd build 
```
Now select the build options using **cmake** Tool 

```bash
sudo cmake -DBUILD_SHARED_LIBS=ON -DCMAKE_BUILD_TYPE=RelWithDebInfo -DUA_NAMESPACE_ZERO=FULL -DUA_ENABLE_AMALGAMATION=ON ..
```
Now use **make** and **make install** commands to build and install open62541 library in your Linux machine
```bash
sudo make
sudo make install
```
Use the **ls** command to see, if **open62541.h** and **open62541.c** files have been created successfully
```bash
ls
```
 
So far so good. So you can see that **open62541.h** and **open62541.c** files have been created successfully in the **build** folder. 

By following the above guide, open62541 is now successfully installed in your linux machine. Now its time to create our first open62541 server and test it. 

## Open62541 (OPC UA in C) Server Installation
In order to install our first open62541 server, we need to create a new **Server** directory. We can create this **Server** directory anywhere in our linux machine, but for the purpose of this guide, we will create it inside open62541 directory. Remember, you have to move out of **build** directory, where we have just finished using **make install** command.
```bash
cd ..
```
Now inside the open62541 directory, create the **Server** directory as mentioned above
```bash
sudo mkdir Server
```
Now copy the **open62541.h** and **open62541.c** from the **build** directory into the **Server** directory.

```bash
sudo cp ./build/open62541.* ./Server/
```

Now go to **Server** directory and see if you can find **open62541.h** and **open62541.c** files
```bash
cd Server
```
Now create **myServer.c** server file using your favourite text editor. (**Nano** is used in this guide)

``` bash
 sudo nano myServer.c
```
Copy the following code into the file

```bash
#include <open62541.h>

#include <signal.h>
#include <stdlib.h>

static volatile UA_Boolean running = true;
static void stopHandler(int sig) {
    UA_LOG_INFO(UA_Log_Stdout, UA_LOGCATEGORY_USERLAND, "received ctrl-c");
    running = false;
}

int main(void) {
    signal(SIGINT, stopHandler);
    signal(SIGTERM, stopHandler);

    UA_Server *server = UA_Server_new();
    UA_ServerConfig_setDefault(UA_Server_getConfig(server));

    UA_StatusCode retval = UA_Server_run(server, &running);

    UA_Server_delete(server);
    return retval == UA_STATUSCODE_GOOD ? EXIT_SUCCESS : EXIT_FAILURE;
}
```
Now save the **myServer.C** file and compile the open62541 server using the following command using **gcc**. (please note that gcc must be installed on your linux machine before using the below mentioned command)
```bash
sudo gcc -std=c99 open62541.c myServer.c -o myServer
```

This is all that is needed for a simple OPC UA server. With the GCC compiler, an executable is created. We can now run the Server by using the following command:

```bash
./myServer
```

After successfully running of the server, you will see the following output on the screen:

```bash
./myServer
[2024-03-23 16:17:05.112 (UTC+0500)] info/eventloop	Starting the EventLoop
[2024-03-23 16:17:05.112 (UTC+0500)] warn/server	AccessControl: Unconfigured AccessControl. Users have all permissions.
[2024-03-23 16:17:05.112 (UTC+0500)] info/server	AccessControl: Anonymous login is enabled
[2024-03-23 16:17:05.112 (UTC+0500)] warn/server	x509 Certificate Authentication configured, but no encrypting SecurityPolicy. This can leak credentials on the network.
[2024-03-23 16:17:05.197 (UTC+0500)] warn/userland	ServerUrls already set. Overriding.
[2024-03-23 16:17:05.197 (UTC+0500)] warn/server	AccessControl: Unconfigured AccessControl. Users have all permissions.
[2024-03-23 16:17:05.197 (UTC+0500)] info/server	AccessControl: Anonymous login is enabled
[2024-03-23 16:17:05.197 (UTC+0500)] warn/server	x509 Certificate Authentication configured, but no encrypting SecurityPolicy. This can leak credentials on the network.
[2024-03-23 16:17:05.197 (UTC+0500)] warn/server	x509 Certificate Authentication configured, but no encrypting SecurityPolicy. This can leak credentials on the network.
[2024-03-23 16:17:05.197 (UTC+0500)] warn/server	Maximum SecureChannels count not enough for the maximum Sessions count
[2024-03-23 16:17:05.197 (UTC+0500)] info/network	TCP	| Listening on all interfaces
[2024-03-23 16:17:05.197 (UTC+0500)] info/network	TCP 4	| Creating server socket for "0.0.0.0" on port 4840
[2024-03-23 16:17:05.197 (UTC+0500)] info/network	TCP 5	| Creating server socket for "::" on port 4840
[2024-03-23 16:17:05.198 (UTC+0500)] info/network	TCP 6	| Creating server socket for "127.0.1.1" on port 484
```
The Server can be stopped with Ctr+C command:
```bash
C[2024-03-23 16:18:27.363 (UTC+0500)] info/userland	received ctrl-c
[2024-03-23 16:18:27.363 (UTC+0500)] warn/eventloop	Timeout during poll
[2024-03-23 16:18:27.363 (UTC+0500)] info/network	TCP 6	| Socket closed
[2024-03-23 16:18:27.363 (UTC+0500)] info/network	TCP 5	| Socket closed
[2024-03-23 16:18:27.363 (UTC+0500)] info/network	TCP 4	| Socket closed
[2024-03-23 16:18:27.463 (UTC+0500)] info/eventloop	Stopping the EventLoop
[2024-03-23 16:18:27.463 (UTC+0500)] info/network	ETH	| Shutting down the ConnectionManager
[2024-03-23 16:18:27.463 (UTC+0500)] info/network	UDP	| Shutting down the ConnectionManager
[2024-03-23 16:18:27.463 (UTC+0500)] info/network	TCP	| Shutting down the ConnectionManager
[2024-03-23 16:18:27.463 (UTC+0500)] info/eventloop	The EventLoop has stopped
[2024-03-23 16:18:27.463 (UTC+0500)] info/session	TCP 0	| SC 0	| Session "Administrator"	| Subscription 0 | Subscription deleted
```
We have  now compiled and run your first OPC UA server. You can go ahead and browse the information model with client. The server is listening on **opc.tcp://localhost:4840**. 

## Documentation

[Documentation of open62541](www.open62541.org)


## Contributing

Pull requests are welcome. For major changes, please open an issue first
to discuss what you would like to change.
