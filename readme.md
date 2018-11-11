# NANO Node Docker stack

<div align="center">
    <img src="nano-node-docker.png" alt="Logo" width='180px' height='auto'/>
</div>

## **Description**

**Install a NANO node on your server with a vast variety of tools in a couple on minutes!** 💫

<table>
	<tr>
        <th>Note</th>
    </tr>
    	<tr>
        <td>
        For hosting a NANO node in the <a href="https://beta.nano.org/" target="_blank">BETA network</a>, checkout the "<a href="https://github.com/lephleg/nano-node-docker/tree/beta"><b>beta</b></a>" branch.
        </td>
    </tr>
</table>

This project will build and deploy the following containers on your Docker host:

<table>
	<tr>
		<th width="200px">Container name</th>
		<th>Description</th>
 	</tr>
 	<tr>
   <td><b>nano-node</b></td>
   		<td>The NANO node created out of the official <a href="https://hub.docker.com/r/nanocurrency/nano/" target="_blank">NANO Docker Image</a>. RPC is enabled but <u>not</u> publicly exposed. (Renamed to "<i>nano-beta-node</i>" for BETA)</td>
 	</tr>
	<tr>
  		<td><b>nano-node-monitor</b></td>
   		<td>The popular NANO Node Monitor PHP application based on <a href="https://hub.docker.com/r/nanotools/nanonodemonitor/" target="_blank">NanoTools's Docker image</a>.</td>
 	</tr>
	<tr>
  		<td><b>nano-node-watchdog</b></td>
   		<td>A custom lightweight watcher container checking on node's health status every hour. Checking code adapted from <a href="https://github.com/dbachm123/nanoNodeScripts" target="_blank">dbachm123's nanoNodeScripts</a>.</td>
 	</tr>
	<tr>
  		<td><b>watchtower</b></td>
   		<td>A process watching all the other containers and automatically applying any updates to their base image. No need to manually upgrade your node anymore.</td>
 	</tr>
</table>

### **SSL Support with Let's Encrypt**

Optionally, if a domain name is available for your host, NANO Node Docker can also serve your monitor securely using HTTPS. If this feature is enabled (using the `-d` argument with the installer), the stack will also include the following containers:

<table>
	<tr>
		<th width="220px">Container name</th>
		<th>Description</th>
 	</tr>
 	<tr>
   <td><b>nginx-proxy</b></td>
   		<td>An instance of the popular Nginx web server running in a reverse proxy setup. Handles the traffic and serves as a gateway to your host.</td>
 	</tr>
	<tr>
  		<td><b>nginx-proxy-letsencrypt</b></td>
   		<td>A lightweight companion container for the nginx-proxy. It allows the creation/renewal of Let's Encrypt certificates automatically.</td>
 	</tr>
</table>

## **Quick Start**

Download or clone the latest release, open a bash terminal and fire up the installation script:

```
$ cd ~ && git clone https://github.com/lephleg/nano-node-docker.git && cd ~/nano-node-docker
$ sudo ./setup.sh -s
```

**That's it!** You can now navigate to your host IP to check your Nano Node Monitor dashboard. **Do not forget to write down** your wallet seed as it appears in the output of the installer.

### Available command flags/arguments

The following flags are available when running the stack installer:

<table>
    <tr>
        <th width="20px">Flag</th>
        <th width="180px">Argument</th>
        <th>Description</th>
    </tr>
    <tr>
        <td><b>-d</b></td>
        <td>your domain name</td>
        <td>Sets the domain name to be used. Required for SSL-enabled setups.</td>
    </tr>
    <tr>
        <td><b>-e</b></td>
        <td>your email address</td>
        <td>Sets your email for Let's Encrypt certificate notifications. Optional for SSL-enabled setups.</td>
    </tr>
    <tr>
        <td><b>-f</b></td>
        <td>-</td>
        <td>Enables fast-syncing by fetching the latest ledger and placing it into <i>/root/Raiblocks/</i> inside <b>nano-node</b> container.</td>
    </tr>
    <tr>
        <td><b>-i</b></td>
        <td>your existing wallet seed</td>
        <td>Imports the passed wallet seed automatically. You can leave this argument blank for security concerns and the installer will guide you to manually import your seed.</td>
    </tr>
    <tr>
        <td><b>-q</b></td>
        <td>-</td>
        <td>Quiet mode. Hides any output.</td>
    </tr>
    <tr>
        <td><b>-s</b></td>
        <td>-</td>
        <td>Prints the unecrypted seed of the node wallet during the setup (<b>WARNING:</b> in most cases you may want to avoid this
            for security purposes).</td>
    </tr>
    <tr>
        <td><b>-t</b></td>
        <td>Docker image tag</td>
        <td>Indicates the preferred tag for the nanocurrency Docker image. Defaults to "latest". Optional.</td>
    </tr>
</table>

### NANO Node CLI bash alias

NANO node runs inside the nano-node container. In order to execute commands from its [Command Line Interface](https://github.com/nanocurrency/raiblocks/wiki/Command-line-interface) you'll have to enter the container or execute them by using the following Docker command:

```
$ docker exec -it nano-node rai_node <command>
```

For convinience the following shorthand alias is set by the installer:

```
$ rai <command>
```

Both of the above formats are interchangeable.

## Examples

### **Install with SSL enabled**

After your DNS records are setup, fire up the installation script with the domain (-d) argument:

```
$ sudo ./setup.sh -d mydomain.com -e myemail@example.com
```

The email (-e) argument is optional and would used by Let's Encrypt to warn you of impeding certificate expiration.

**Done!** Navigate to your domain name to check your Nano Node Monitor Dashboard over HTTPS!

### **Import existing wallet seed**

There are cases you already have a wallet seed you'd like to use in your node. You can use the import flag (`-i`) to pass a NANO wallet seed and let the installer import it for you.

```
$ sudo ./setup.sh -i 51724DB3B4AE224950CA35D7D9365BD4DE69BB53C57BFC4D2E65807C5F18EDC0
```

**Warning:** Supplying an import seed with OVERWRITE any existing ones in any NANO nodes already set up by NANO Node Docker!

**Note:** It's understandable if for security reason you'd like to avoid passing your seed to a third-party tool. In that case, you can leave the argument empty and installer will supply you with the appropriate NANO node CLI commands to import your seed manually.

### **Install with a different NANO node image**

In some cases (like in the BETA network) you may want to use a different Docker image tag for your node, other than the default "latest":

```
$ sudo ./setup.sh -t V16.0RC2
```

**Note:** For the mainnet, you are **strongly advised** to stick with the "latest" image tag. Do otherwise, only if instructed by the NANO core team. 

### **Combining installer flags**

All the installer flags can be chained, so you can easily combine them like this:

```
$ sudo ./setup.sh -sfd mydomain.com -e myemail@example.com
```

(_display seed, apply fast-sync and use Let's Encrypt with your email supplied_)

<div align="center">
    <img src="screenshot.png" alt="Screenshot" width='680px' height='auto'/>
</div>

## Self-configurable Installation

Please check the [wiki](https://github.com/lephleg/nano-node-docker/wiki)
 for more detailed instructions on how to manually self-configure NANO Node Docker.

## **Credits**

* **[Nanocurrency](https://github.com/nanocurrency/raiblocks)**
* **[NANO Node Monitor](https://github.com/NanoTools/nanoNodeMonitor)**
* **[nanoNodeScripts](https://github.com/dbachm123/nanoNodeScripts)**
* **[jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)**
* **[JrCs/docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion)**
* **[v2tec/watchtower](https://github.com/v2tec/watchtower)**

## **Support**

If you really liked this tool, **just give this project a star** ⭐️ so more people get to know it. Cheers! :)
